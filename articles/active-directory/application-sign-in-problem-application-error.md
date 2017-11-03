---
title: "Erro na página de uma aplicação depois de iniciarem sessão | Microsoft Docs"
description: "Como resolver problemas com o Azure AD de início de sessão quando a própria aplicação emite um erro"
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
ms.openlocfilehash: a8cd93256f79ece268ec3411dfbdf590f4b24447
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Erro na página de uma aplicação depois de iniciar sessão

Neste cenário, do Azure AD iniciou a sessão do utilizador no, mas a aplicação apresenta um erro não permitindo que o utilizador concluir com êxito o início de sessão no fluxo. Neste cenário, a aplicação não está a aceitar o problema de resposta pelo Azure AD.

Existem algumas razões possíveis motivo pelo qual a aplicação não aceitou a resposta do Azure AD. Se o erro na aplicação não é claro que deve saber o que está em falta na resposta, em seguida:

-   Se a aplicação está a galeria do Azure AD, certifique-se de que seguiu todos os passos no artigo [como depurar baseados em SAML-início de sessão único para aplicações no Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Utilizar uma ferramenta como o [Fiddler](http://www.telerik.com/fiddler) para capturar pedido SAML, resposta SAML e SAML token.

-   Partilhe a resposta SAML com o fabricante da aplicação para saber o que está em falta.

## <a name="missing-attributes-in-the-saml-response"></a>Atributos em falta na resposta SAML

Para adicionar um atributo na configuração do Azure AD para serem enviados na resposta do Azure AD, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Clique em **ver e editar todas as outras utilizador atributos em** o **atributos de utilizador** secção para editar os atributos a ser enviadas para a aplicação no SAML token quando o utilizador iniciar sessão.

   Para adicionar um atributo:

   * Clique em **adicionar atributo**. Introduza o **nome** e selecione o **valor** na lista pendente.

   * Clique em **guardar.** Pode ver o novo atributo na tabela.

9.  Guarde a configuração.

Próxima vez que o utilizador inicia sessão na aplicação do Azure AD enviar o novo atributo na resposta SAML.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>A aplicação espera um formato ou valor de identificador de utilizador diferente

O início de sessão para a aplicação está a falhar porque a resposta SAML faltam atributos, tais como as funções ou porque a aplicação é era esperado um formato diferente para o atributo EntityID.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Adicione um atributo na configuração de aplicação do Azure AD:

Para alterar o valor do identificador de utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Sob o **atributos de utilizador**, selecione o identificador único para os utilizadores no **identificador de utilizador** pendente.

## <a name="change-entityid-user-identifier-format"></a>Alterar o formato de EntityID (identificador de utilizador)

Se a aplicação espera outro formato para o atributo EntityID. Em seguida, não poderá selecionar o formato de EntityID (utilizador Identifier) que envia do Azure AD para a aplicação na resposta após a autenticação de utilizador.

Selecione de AD do Azure o formato para o atributo de NameID (identificador de utilizador) com base no valor selecionado ou o formato solicitado pela aplicação na AuthRequest SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>A aplicação espera um método de assinatura diferente para a resposta SAML

Para alterar as partes do SAML token estejam assinadas digitalmente pelo Azure Active Directory. Siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Clique em **Mostrar avançadas definições de assinatura de certificado** sob o **certificado de assinatura de SAML** secção.

9.  Selecione as adequadas **assinatura opção** esperado pela aplicação:

  * Resposta SAML do início de sessão

  * Resposta SAML do início de sessão e asserção

  * Início de sessão de asserção SAML

Próxima vez que o utilizador inicia sessão na aplicação do Azure AD, inicie a parte da resposta SAML selecionada.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>A aplicação espera o algoritmo de assinatura a ser SHA-1

Por predefinição, o Azure AD assina o token SAML com o algoritmo de segurança mais. Alterar o algoritmo de início de sessão para SHA-1 não é recomendado, a menos que o requerido pela aplicação.

Para alterar o algoritmo de assinatura, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Clique em **Mostrar avançadas definições de assinatura de certificado** sob o **certificado de assinatura de SAML** secção.

9.  Selecione SHA-1, o **algoritmo de assinatura**.

Próxima vez que o utilizador inicia sessão na aplicação do Azure AD, inicie o token SAML utilizando o algoritmo SHA-1.

## <a name="next-steps"></a>Passos seguintes
[Como depurar baseados em SAML-início de sessão único para aplicações no Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
