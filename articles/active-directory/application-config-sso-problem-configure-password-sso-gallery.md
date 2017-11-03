---
title: "Problema de configuração de palavra-passe-início de sessão único para uma aplicação de galeria do Azure AD | Microsoft Docs"
description: "Compreender a letra de pessoas problemas comuns quando configurar a palavra-passe-início de sessão único para aplicações que já estão listadas na Galeria de aplicações do Azure AD"
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
ms.openlocfilehash: 58d29996a922fac6d295e753ba5d66d32e745a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema de configuração de palavra-passe-início de sessão único para uma aplicação de galeria do Azure AD

Este artigo ajuda-o a compreender a letra de pessoas problemas comuns quando configurar **palavra-passe-início de sessão único** com uma aplicação de galeria do Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>As credenciais são preenchidas, mas a extensão não submetê-las

Isto acontece normalmente se o fabricante da aplicação foi alterado o início de sessão página recentemente para adicionar um campo, alterar um identificador subjacente que é utilizado para detetar os campos de nome de utilizador e palavra-passe, ou modificar a forma como o início de sessão experiência funciona para a respetiva aplicação. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicação para rapidamente resolver estes problemas.

Embora a Microsoft tem tecnologias para detetar automaticamente quando estes integrações interromper, mas por vezes, mas não é possível localizar estes problemas de imediato ou poderem demorar algum tempo para corrigir. No caso de quando um destes integrações não funcione corretamente, agradecemos se tiver aberto um incidente de suporte para que possamos corrigi-lo mais rapidamente possível.

Além disto, **se forem in contact with fornecedor desta aplicação,** **envie-os nossa forma** para que possa trabalhar com os mesmos nativamente integrar a sua aplicação no Azure Active Directory. Pode enviar o fornecedor para a [listar a aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) obtê-los foi iniciado.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>As credenciais estão preenchidas e submetidas, mas a página indica que as credenciais estão incorretas

Para resolver este problema, verifique o seguinte:

-   Tem do utilizador tentar primeiro **iniciar sessão diretamente no Web site da aplicação** com as credenciais armazenadas para os mesmos.

  * Se funciona, tem, em seguida, o utilizador, clique no **atualizar as credenciais** botão no **mosaico de aplicação** no **aplicações** secção o [painel de acesso de aplicação](https://myapps.microsoft.com/) para atualizá-las para o nome de utilizador de trabalho conhecidas mais recentes e a palavra-passe.

   * Se o utilizador ou outro administrador atribuída as credenciais para este utilizador, localizar o utilizador ou a atribuição do grupo de aplicações, navegando até o **utilizadores e grupos** separador da aplicação, selecionando a atribuição e clicando a **credenciais de atualização** botão.

-   Se o utilizador atribuído as suas próprias credenciais, tem do utilizador **verifique Certifique-se de que a palavra-passe não expirou na aplicação** e se assim for, **atualizar a palavra-passe expirada** pelo início de sessão na aplicação diretamente.

   * Depois da palavra-passe tiver sido atualizada na aplicação, pedir ao utilizador clicar no **atualizar as credenciais** botão no **mosaico de aplicação** no **aplicações** secção o [painel de acesso de aplicação](https://myapps.microsoft.com/) para atualizá-las para o nome de utilizador de trabalho conhecidas mais recentes e a palavra-passe.

   * Se o utilizador ou outro administrador atribuída as credenciais para este utilizador, localizar o utilizador ou a atribuição do grupo de aplicações, navegando até o **utilizadores e grupos** separador da aplicação, selecionando a atribuição e clicando a **credenciais de atualização** botão.

-   Tem do utilizador a atualizar a extensão de browser do painel de acesso ao seguir os passos abaixo o [como instalar a extensão de Browser do painel de acesso](#how-to-install-the-access-panel-browser-extension) secção.

-   Certifique-se de que a extensão de browser do painel de acesso está em execução e ativado no browser do utilizador.

-   Certifique-se de que os utilizadores não estão a tentar iniciar sessão para a aplicação do painel de acesso no **modo privado, inPrivate ou incognito**. A extensão do painel de acesso não é suportada nestes modos.

No caso de este não funciona, poderia ser as maiúsculas e minúsculas não tiver ocorrido uma alteração no lado de aplicação que foi temporariamente quebrada integração da aplicação com o Azure AD. Por exemplo, isto pode ocorrer quando o fornecedor da aplicação apresenta um script na respetiva página que funciona de forma diferente para manuais vs automatizada de entrada, o que faz com que a atribuição de integração, como o nosso própria, para interromper. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicação para rapidamente resolver estes problemas.

Embora a Microsoft tem tecnologias para detetar automaticamente quando estes integrações interromper, mas por vezes, mas não é possível localizar estes problemas de imediato ou poderem demorar algum tempo para corrigir. No caso de quando um destes integrações não funcione corretamente, agradecemos se tiver aberto um incidente de suporte para que possamos corrigi-lo mais rapidamente possível.

Além disto, **se forem in contact with fornecedor desta aplicação,** **envie-os nossa forma** para que possa trabalhar com os mesmos nativamente integrar a sua aplicação no Azure Active Directory. Pode enviar o fornecedor para a [listar a aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) obtê-los foi iniciado.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A extensão funciona no Chrome e Firefox, mas não no Internet Explorer

Existem duas causas principais para este problema:

-   Consoante as definições de segurança ativadas no Internet Explorer, se o Web site não é parte de um **zona fidedigna**, por vezes, nosso script ser impedidos de execução para a aplicação.

  *  Para resolver este problema, instruir o utilizador a **adicionar Web site da aplicação** para o **Sites fidedignos** lista dentro do respetivo **definições de segurança do Internet Explorer**. Pode enviar os seus utilizadores para o [como adicionar um site ao meu lista de sites fidedignos](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) artigo para obter instruções detalhadas.

-   Em circunstâncias raras, validação de segurança do Internet Explorer, por vezes, pode fazer com que a página para carregar mais lentamente do que a execução do nosso script.

   * Infelizmente, esta situação pode variar consoante a versão do browser, a velocidade do computador ou o site visitada. Neste caso, sugerimos que contacte o suporte para que possamos corrigir a integração para esta aplicação específica.

Além disto, **se forem in contact with fornecedor desta aplicação,** **envie-os nossa forma** para que possa trabalhar com os mesmos nativamente integrar a sua aplicação no Azure Active Directory. Pode enviar o fornecedor para a [listar a aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) obtê-los foi iniciado.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Verifique se a página de início de sessão da aplicação foi recentemente alterada ou necessita de um campo adicional

Se a página de início de sessão da aplicação mudou significativamente, por vezes, isto faz com que a nossa integrações interromper. Um exemplo desta situação é quando um fornecedor da aplicação adiciona um início de sessão no campo, uma captcha ou multi-factor authentication as suas experiências. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicação para rapidamente resolver estes problemas.

Embora a Microsoft tem tecnologias para detetar automaticamente quando estes integrações interromper, mas por vezes, mas não é possível localizar estes problemas de imediato. Caso contrário podem demorar algum tempo para corrigir. No caso de quando um destes integrações não funcione corretamente, agradecemos abrir um incidente de suporte para que possamos corrigi-lo mais rapidamente possível.

Além disto, **se forem in contact with fornecedor desta aplicação,** **envie-os nossa forma** para que possa trabalhar com os mesmos nativamente integrar a sua aplicação no Azure Active Directory. Pode enviar o fornecedor para a [listar a aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) obtê-los foi iniciado.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga os passos abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) dos browsers suportados e início de sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação de palavra-passe SSO** no painel de acesso.

3.  Na linha de pedir instalar o software, selecione **instalar agora**.

4.  Com base no seu browser, direcionado para a hiperligação de transferência. **Adicionar** a extensão para o seu browser.

5.  Se o browser pede-lhe, selecione como **ativar** ou **permitir** a extensão.

6.  Uma vez instalado, **reiniciar** a sessão do browser.

7.  Inicie sessão no painel de acesso e veja se pode **iniciar** as aplicações de SSO de palavra-passe

Também pode transferir a extensão para Chrome e Firefox a partir de hiperligações diretas abaixo:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão de painel de acesso de Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)

