---
title: "O que é o painel de acesso no Azure Active Directory? | Microsoft Docs"
description: "Saiba como utilizar variações do painel de acesso (web browser, aplicação Android, aplicação iPhone e iPad) para aceder a aplicações SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a63e2d583e1203708e49c0fcef99876d2055343a
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="what-is-the-access-panel"></a>O que é o painel de acesso?

O painel de acesso é um portal baseado na web. Permite que um utilizador com um trabalho ou conta profissional no Azure Active Directory para ver e iniciar a aplicações baseadas na nuvem um administrador do Azure AD concedeu-lhes acesso a. Também pode utilizar grupos self-service e capacidades de gestão de aplicações através do painel de acesso.

O painel de acesso separado do portal do Azure e faz a não lhe ter uma subscrição do Azure.

![Painel de Acesso][1]

O painel de acesso permite-lhe editar algumas das suas definições de perfil, incluindo a capacidade para:

- Alterar a palavra-passe associada a uma conta escolar ou profissional

- Editar definições de reposição de palavra-passe

- Editar definições de contacto e preferências relacionadas com a autenticação multifator (para contas que foram necessário utilizá-lo por um administrador)

- Ver detalhes, tais como o ID de utilizador, alternativa dispositivos e mobile e office números de telefone e e-mail, de conta

- Ver e iniciar a aplicações baseadas na nuvem que o administrador do Azure AD concedeu-lhes acesso a. Para obter mais informações sobre o painel de acesso de perspetiva dos utilizadores, consulte através do painel de acesso. 

- Self-gerir grupos. Mais especificamente, o administrador pode criar e gerir grupos de segurança e as associações de grupo de segurança de pedido no Azure AD. Para obter mais informações, consulte [gestão de grupos self-service para utilizadores no Azure AD](active-directory-accessmanagement-self-service-group-management.md) e [gerir os grupos](active-directory-manage-groups.md).




## <a name="accessing-the-access-panel"></a>Aceder ao painel de acesso

Pode aceder ao painel de acesso, visitando o seguinte URL num browser:`http://myapps.microsoft.com`

Se tiver personalizado uma imagem corporativa configurado para a sua página de início de sessão, pode carregar esta imagem corporativa, acrescentando o domínio da sua organização para o final do URL:`http://myapps.microsoft.com/<your domain>.com`

Neste caso, pode utilizar qualquer nome de domínio do Active Directory ou verificado que tenha sido configurado no portal do Azure.

![Nome de domínio do Wingtip Toys][2]  

Terá de distribuir o URL para todos os utilizadores que irá iniciar sessão para aplicações que estão integradas com o Azure AD.

## <a name="authentication"></a>Autenticação

Para alcançar o painel de acesso, tem de ser autenticado através de uma conta escolar ou profissional no Azure AD. Pode ser autenticado para o Azure AD diretamente. Em alternativa, se uma organização tiver configurado a Federação ao utilizar serviços de Federação do Active Directory (AD FS) ou outras tecnologias, pode ser autenticada pelo Windows Server Active Directory.

Se tiver uma subscrição do Azure ou do Office 365 e utiliza o portal do Azure ou uma aplicação do Office 365, pode ver a lista de aplicações sem iniciar sessão novamente. Não são autenticados se lhe for pedido que inicie sessão com o nome de utilizador e palavra-passe para a sua conta no Azure AD. Se a sua organização tiver configurado a Federação, escrever o nome de utilizador é suficiente.

Quando são autenticados, pode interagir com as aplicações que o administrador tem integrado com o diretório. Para saber como integrar aplicações com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Requisitos de Web browsers

No mínimo, o painel de acesso necessita de um browser que suporte JavaScript e ativou CSS. Para o utilizador iniciar sessão no aplicações através do baseada em palavra-passe-início de sessão único (SSO), a extensão do painel de acesso tem de estar instalada no seu browser. A extensão é transferida automaticamente quando seleciona uma aplicação que está configurada para SSO baseada em palavra-passe.

A extensão do painel de acesso está atualmente disponível para o Internet Explorer 8 e posteriores, limite, o Chrome e o Firefox browsers.

## <a name="mobile-app-support"></a>Suporte de aplicações móveis

A equipa do Azure Active Directory publica a minha aplicação móvel de aplicações. Quando instalar a aplicação, pode iniciar sessão aplicações de SSO baseada em palavra-passe em dispositivos iOS e Android.

> [!NOTE]
> Pode iniciar sessão aplicações que suportam a Federação com o Azure AD (incluindo o Salesforce, Google Apps, Dropbox, caixa, Concur, Workday, Office 365 e 70 mais do que outras pessoas) em praticamente qualquer browser web, em qualquer dispositivo, sem necessitar de uma aplicação de plug-in ou móvel. Todos os outros [experiências do painel de acesso](https://myapps.microsoft.com/) não também requerem a minha aplicação móvel de aplicações a ser utilizada num dispositivo móvel.
>
>

### <a name="my-apps-for-android"></a>Minhas aplicações para Android

As minhas aplicações para Android é suportada em qualquer dispositivo Android que está a executar a versão Android 4.1 e posterior.  
Está disponível no [loja do Google Play](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Minhas aplicações para Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Minhas aplicações para iPhone e iPad

As minhas aplicações para iOS é suportada em qualquer iPhone ou iPad, que está a executar a versão do iOS 7 e posterior.  
Está disponível no [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Minhas aplicações para iOS][4]    



## <a name="managed-browser-for-my-apps"></a>Browser gerido para as minhas aplicações

As minhas aplicações também está integrada no Browser gerido do Intune. O Intune Managed Browser para dispositivos iOS e Android desempenha um papel chave garantir que permanecem proteger dados em dispositivos móveis. Permite-lhe possam ver e navegue páginas web que poderão conter informações da empresa e fornece uma experiência de navegação na web segura.  
Encontrará acesso rápido às minhas aplicações na sua home page de Browser gerido e no seu marcadores, dando-lhe menos cliques para acederem a qualquer aplicação que pretende aceder.

Está disponível no [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Browser Mananged para as minhas aplicações][5]    





## <a name="tips-for-testing-the-user-experience"></a>Sugestões para a experiência de utilizador de teste

Se for um administrador do Azure e tem sessão iniciada portal do Azure, utilizando uma conta no diretório, que é automaticamente sessão painel de acesso como a sua conta atual. Neste caso, pode ver todas as aplicações que tenham sido atribuídas para si.

**Para testar como um *diferentes* conta de utilizador:**

1. Clique no menu de utilizador no canto superior direito do portal do Azure ou o painel de acesso e, em seguida, selecione **terminar sessão**. 
2. Vá para o [painel de acesso](http://myapps.microsoft.com).
3. Na página de início de sessão, escreva o nome de utilizador e palavra-passe para a conta no seu diretório que pretende testar.


## <a name="starting-applications"></a>Início de aplicações

Vários tipos de aplicações podem aparecer no painel de acesso.

### <a name="office-365-applications"></a>Aplicações do Office 365

Se a sua organização está a utilizar aplicações do Office 365 e são licenciados para os mesmos, as aplicações do Office 365 são apresentados no painel de acesso.

Ao clicar um mosaico de aplicação para uma aplicação do Office 365, são redirecionado para a aplicação e inicie automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicações de terceiros da Microsoft e configuradas com o SSO baseada em Federação

O administrador pode adicionar aplicações na secção do portal do Azure Active Directory com o modo SSO definido como **do Azure AD Single Sign-On**. Só pode ver estas aplicações, se o administrador tem explicitamente concedidos que acesso às aplicações.

Ao clicar num mosaico para uma destas aplicações, são redirecionados e inicie automaticamente para a aplicação.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO baseada em palavra-passe sem aprovisionamento de identidade

O administrador pode adicionar aplicações na secção do portal do Azure Active Directory com o modo SSO definido como **baseada em palavra-passe Single Sign-On**. Todos os utilizadores no diretório podem ver todas as aplicações que tenham sido configuradas neste modo.

A primeira vez, clicar num mosaico para uma destas aplicações, lhe for pedido para instalar o plug-in de SSO de palavra-passe para o Internet Explorer ou o Chrome. A instalação poderá requerer que reinicie o seu browser. Quando regressar ao painel de acesso e clique no mosaico de aplicação novamente, lhe for pedido um nome de utilizador e palavra-passe para a aplicação. Quando introduzir o nome de utilizador e palavra-passe, estas credenciais são armazenadas em segurança e ligadas à sua conta no Azure AD.

Da próxima vez que clicar no mosaico de aplicação, que é automaticamente iniciou sessão na aplicação.  
Não tem de introduzir as suas credenciais novamente e ou instalar o plug-in de SSO de palavra-passe.

Se tem alterado as suas credenciais na aplicação de terceiros de destino, tem também de atualizar as suas credenciais são armazenadas no Azure AD. 

**Para atualizar as credenciais:**

1. Selecione o ícone no mosaico aplicações.
2. Selecione **atualizar credenciais** reintroduzir o nome de utilizador e palavra-passe para a aplicação.


### <a name="password-based-sso-with-identity-provisioning"></a>SSO baseada em palavra-passe com o aprovisionamento de identidade

O administrador pode adicionar aplicações a **do Active Directory** secção do portal do Azure com o modo SSO definido como **baseada em palavra-passe Single Sign-On**, juntamente com o aprovisionamento de identidade.

Na primeira vez, clique um mosaico de aplicação para uma destas aplicações, se lhe for pedido para instalar o **SSO de palavra-passe Plug-in para o Internet Explorer ou o Chrome**. A instalação poderá requerer que reinicie o seu browser.  
Quando regressar ao painel de acesso e clique novamente no mosaico de aplicação, que é automaticamente iniciou sessão na aplicação.

Algumas aplicações podem implicar a sua palavra-passe no primeiro início de sessão. Se tem alterado as suas credenciais na aplicação de terceiros de destino, tem também de atualizar as credenciais que são armazenadas no Azure AD. 

**Para atualizar as credenciais:**

1. Selecione o ícone no mosaico aplicações.
2. Selecione **atualizar credenciais** reintroduzir o nome de utilizador e palavra-passe para a aplicação.


### <a name="application-with-existing-sso-solutions"></a>Aplicações com soluções de SSO existentes

Para configurar o SSO para uma aplicação, o portal do Azure fornece uma terceira opção, denominada **existente Single Sign-On**. Esta opção permite que o administrador para criar uma ligação para uma aplicação e coloque-a no painel de acesso para utilizadores selecionados.

Por exemplo, se uma aplicação estiver configurada para autenticar os utilizadores através do AD FS 2.0, o administrador pode utilizar o **existente Single Sign-On** opção para criar uma ligação ao mesmo no painel de acesso. Quando acedem a ligação, são autenticada através do AD FS 2.0 ou qualquer SSO solução existente que a aplicação fornece.


## <a name="next-steps"></a>Passos seguintes

- Para ver uma lista de todos os tópicos relacionados com a gestão de aplicações, consulte o [índice de artigos da gestão de aplicações no Azure Active Directory](active-directory-apps-index.md).
 
- Para saber como integrar uma aplicação SaaS com o Azure AD, consulte o [lista de tutoriais sobre como integrar aplicações SaaS](active-directory-saas-tutorial-list.md).
 
- Para saber mais sobre a gestão de aplicações com o Azure AD, consulte o [introdução ao acesso de aplicação único de início de sessão e gerir com o Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Para saber mais sobre o aprovisionamento de utilizador, consulte [automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
