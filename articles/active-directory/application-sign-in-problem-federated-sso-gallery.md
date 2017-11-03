---
title: "Problemas em iniciar sessão a uma aplicação de galeria configurada para Federado de sessão único-| Microsoft Docs"
description: "Orientações para os erros específicos quando iniciar sessão uma aplicação que configurou para baseados em SAML federado-início de sessão único com o Azure AD"
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
ms.openlocfilehash: 0fc5a8eb3d033d60bf6082d61bf1698924ab91c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemas em iniciar sessão a uma aplicação de galeria configurada para federado-início de sessão único

Para resolver o problema, tem de verificar a configuração de aplicação no Azure AD como seguir:

-   Seguiu todos os passos de configuração para a aplicação de galeria do Azure AD.

-   O identificador e o URL de resposta configurado no AAD corresponde a estes valores esperados na aplicação

-   Foi atribuído aos utilizadores para a aplicação

## <a name="application-not-found-in-directory"></a>Não foi encontrada no diretório de aplicação

*AADSTS70001 de erro: A aplicação com o identificador 'https://contoso.com' não foi encontrada no diretório*.

**Uma causa possível**

O emissor atributo envia da aplicação para o Azure AD no pedido SAML não corresponde ao valor de identificador configurado na aplicação do Azure AD.

**Resolução**

Certifique-se de que o atributo de emissor no pedido SAML correspondência de identificador de valor configurado no Azure AD:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Aceda a **domínios e URLs** secção. Certifique-se de que o valor na caixa de texto de identificador é o valor para o valor do identificador apresentado o erro de correspondência.

Depois de ter atualizado o valor do identificador no Azure AD e -correspondência envia o valor pela aplicação no pedido SAML, deverá conseguir iniciar sessão para a aplicação.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não correspondem aos endereços resposta configurados para a aplicação.

*AADSTS50011 de erro: O endereço de resposta 'https://contoso.com' não correspondem aos endereços resposta configurados para a aplicação*

**Uma causa possível**

O valor de AssertionConsumerServiceURL no pedido SAML não corresponde ao valor do URL de resposta ou padrão configurado no Azure AD. O valor de AssertionConsumerServiceURL no pedido SAML é o URL, consulte o erro.

**Resolução**

Certifique-se de que o valor de AssertionConsumerServiceURL no pedido SAML correspondência o URL de resposta valor configurado no Azure AD.

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Aceda a **domínios e URLs** secção. Certifique-se ou Atualize o valor na caixa de texto de URL de resposta, de corresponder ao valor AssertionConsumerServiceURL no pedido SAML.  
    * Se não vir a caixa de texto de resposta de URL, selecione o **Mostrar avançadas definições de URL** caixa de verificação.

Depois de ter atualizado o valor do URL de resposta no Azure AD e -correspondência envia o valor pela aplicação no pedido SAML, deverá conseguir iniciar sessão para a aplicação.

## <a name="user-not-assigned-a-role"></a>Não atribuído uma função de utilizador

*AADSTS50105 de erro: O utilizador com sessão iniciada 'brian@contoso.com' não está atribuído a uma função para a aplicação*.

**Uma causa possível**

Não foi concedido ao utilizador acesso à aplicação no Azure AD.

**Resolução**

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de correio eletrónico** do utilizador que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **utilizador** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do utilizador ou logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais do que um utilizador**, tipo noutra **nome completo** ou **endereço de correio eletrónico** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação para os utilizadores selecionados.

Após um curto período de tempo, os utilizadores que selecionou conseguir iniciar estas aplicações utilizando os métodos descritos na secção de descrição de solução.

## <a name="not-a-valid-saml-request"></a>Não um pedido de SAML válido

*AADSTS75005 de erro: O pedido não é uma mensagem de protocolo Saml2 válida.*

**Uma causa possível**

Azure AD não suporta o pedido de SAML enviada pela aplicação para o início de sessão único. Alguns problemas comuns são:

-   Campos necessários no pedido SAML em falta

-   Método de pedido codificado de SAML

**Resolução**

1.  Capture pedido SAML. Siga o tutorial [como depurar baseados em SAML-início de sessão único para aplicações no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) para aprender a capturar o pedido SAML.

2.  Contacte o fabricante da aplicação e a partilha:

   -   Pedido SAML

   -   [Requisitos de protocolo do Azure AD único início de sessão SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Eles devem validar suportam a implementação de SAML do Azure AD para o início de sessão único.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Nenhum recurso na lista de requiredResourceAccess

*AADSTS65005 de erro: A aplicação de cliente tiver solicitado acesso a recursos ' 00000002-0000-0000-c000-000000000000'. Este pedido falhou porque o cliente não especificou este recurso na lista de requiredResourceAccess*.

**Uma causa possível**

O objeto de aplicação está danificado.

**Resolução: opção 1**

Para resolver o problema, adicione o valor de identificador exclusivo na configuração do Azure AD. Para adicionar um valor de identificador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação tiver configurado o início de sessão único.

7.  Depois da aplicação carrega, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação

8.  Sob o **domínio e o URL** secção, verifique no **Mostrar avançadas definições de URL**.

9.  no **identificador** um identificador exclusivo para a aplicação do tipo de caixa de texto.

10. **Guardar** a configuração.


**Opção de resolução de 2**

Se a opção 1 acima não resolver o problema, tente remover a aplicação a partir do diretório. Em seguida, adicionar e reconfigure a aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único

7.  Clique em **eliminar** à parte superior esquerda da aplicação **descrição geral** painel.

8.  Atualizar do Azure AD e adicionar a aplicação a partir da galeria do Azure AD. Em seguida, Configure a aplicação

<span id="_Hlk477190176" class="anchor"></span>Depois de reconfigurar a aplicação, deverá conseguir iniciar sessão para a aplicação.

## <a name="certificate-or-key-not-configured"></a>Certificado ou chave não configurado

*Erro AADSTS50003: Nenhuma chave de assinatura configurado.*

**Uma causa possível**

O objeto de aplicação está danificado e do Azure AD não reconhece o certificado configurado para a aplicação.

**Resolução**

Para eliminar e criar um novo certificado, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

 * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Clique em **criar novo certificado** sob o **SAML certificado de assinatura** secção.

9.  Selecione a data de expiração. Em seguida, clique em **guardar.**

10. Verifique **tornar o novo certificado ativa** para substituir o certificado do Active Directory. Em seguida, clique em **guardar** na parte superior do painel e aceitar para ativar o certificado de rollover.

11. Sob o **certificado de assinatura de SAML** secção, clique em **remover** para remover o **não utilizados** certificado.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as afirmações SAML enviadas para uma aplicação

Para saber como personalizar as afirmações de atributo SAML enviadas para a sua aplicação, consulte [afirmações mapeamento no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Como depurar baseados em SAML-início de sessão único para aplicações no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)
