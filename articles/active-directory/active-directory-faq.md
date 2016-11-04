---
title: FAQ do Azure Active Directory | Microsoft Docs
description: As FAQ do Azure Active Directory fornecem respostas a perguntas, juntamente com o acesso ao Azure e Azure Active Directory, gestão de palavras-passe e acesso à aplicação.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/16/2016
ms.author: markusvi

---
# FAQ do Azure Active Directory
O Azure Active Directory é uma solução Identidade como um Serviço (IDaaS) completa que abrange todos os aspetos relativos à identidade, gestão de acesso e segurança.

Para obter mais detalhes, consulte [O que é o Azure Active Directory?](active-directory-whatis.md).

## Aceder ao Azure e Azure Active Directory
**P: Por que motivo recebo “Não foram encontradas subscrições” ao tentar aceder ao Azure AD no Portal Clássico do Azure (https://manage.windowsazure.com)?**

**R:** O acesso ao Portal Clássico do Azure requer que cada utilizador tenha permissões numa subscrição do Azure. Se tiver uma subscrição paga do Office 365 ou do Azure AD, vá para [http://aka.ms/accessAAD](http://aka.ms/accessAAD) para uma ativação única. Caso contrário, terá de ativar uma [versão de avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/) completa ou uma subscrição paga. 

Para obter mais detalhes, consulte:

* [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerir o diretório da subscrição do Office 365 no Azure](active-directory-manage-o365-subscription.md)

- - -
**P: Qual é a relação entre o Azure AD, o Office 365 e o Azure?**

**R:** O Azure Active Directory fornece-lhe capacidades de identidade e de acesso comuns a todos os serviços online da Microsoft. Se estiver a utilizar o Office 365, o Microsoft Azure, o Intune ou outros, já está a utilizar um Azure AD para ativar o início de sessão e gestão de acesso a todos esses serviços. 

Na realidade, todos os utilizadores que tiver ativado para os serviços online da Microsoft são definidos como contas de utilizador numa ou mais instâncias do Azure AD. Pode ativar essas contas para obter gratuitamente capacidades do Azure AD, como o acesso a aplicações na nuvem.

Além disso, os serviços pagos do Azure AD (por exemplo, Azure AD Basic, Premium, EMS, etc.) completam outros serviços online, como o Office 365 e o Microsoft Azure com soluções abrangentes de gestão empresarial e de segurança.

- - -
## Introdução ao Azure AD Híbrido
**P: Como ligar o meu diretório no local ao Azure AD?**

**R:** Pode ligar o diretório no local ao Azure AD com o **Azure AD Connect**. 

Para obter mais detalhes, consulte o artigo [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

- - -
**P: Como posso configurar o SSO entre o meu diretório no local e as minhas aplicações na nuvem?**

**R:** Só tem de configurar o SSO entre o seu diretório no local e o Azure AD. Enquanto aceder às suas aplicações na nuvem através do Azure AD, o serviço orienta automaticamente os utilizadores para uma autenticação correta com as suas credenciais no local.

A implementação do SSO no local poderá ser feita facilmente com soluções de federação, tais como o ADFS ou ao configurar a sincronização hash de palavras-passe. Pode facilmente implementar ambas as opções utilizando o assistente de configuração do Azure AD Connect.

Para obter mais detalhes, consulte o artigo [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

- - -
**P: O Azure Active Directory fornece um portal personalizado aos utilizadores da minha organização?**

**R:** Sim, o Azure Active Directory fornece o acesso ao [Painel de Acesso do Azure AD](http://myapps.microsoft.com) para acesso a utilizadores pessoalizados e a aplicações. Se for um cliente do Office 365, poderá encontrar muitas das mesmas funcionalidades no portal do Office 365. 

Para mais informações, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

- - -
**P: O Azure AD ajuda-me a gerir a minha infraestrutura no local?**

**R:** Sim. A edição Premium do Azure AD fornece o **Connect Health**. O Azure AD Connect Health ajuda a monitorizar e obter informações sobre a sua infraestrutura de identidade no local, bem como sobre os serviços de sincronização.  

Para obter mais detalhes, consulte [Monitorizar os serviços de infraestrutura de identidade no local e sincronização na nuvem](active-directory-aadconnect-health.md).  

- - -
## Gestão de palavras-passe
**P: Posso utilizar a repetição de escrita de palavras-passe do Azure AD sem sincronizar a palavra-passe? (ou seja, gostaria de utilizar Azure AD SSPR com repetição de escrita de palavras-passe, mas não desejo as minhas palavras-passe armazenadas na nuvem.)**

**R:** Não tem de sincronizar as palavras-passe do AD no Azure AD para ativar a repetição de escrita de palavras-passe. Num ambiente federado, o SSO do Azure AD depende do diretório no local para autenticar o utilizador. Este cenário não requer que a palavra-passe no local seja controlada no Azure AD.

- - -
**P: Quanto tempo demora a repetição de escrita de uma palavra-passe no AD no local?**

**A:** a repetição de palavras-passe é feita em tempo real. 

Para obter mais detalhes, consulte [Introdução à Gestão de Palavras-passe](active-directory-passwords-getting-started.md) 

- - -
**P: posse utilizar a repetição de palavras-passe com palavras-passe geridas por um administrador?**

**R:** Sim, se tiver ativado a repetição de palavras-passe, as operações de palavras-passe feitas por um administrador são repetidas no ambiente local.  

Para obter mais respostas a perguntas relacionadas com palavras-passe, consulte [Perguntas Mais Frequentes da Gestão de Palavras-passe](active-directory-passwords-faq.md).

- - -
## Acesso à aplicação
**P: Onde posso encontrar uma lista das aplicações previamente integradas ao Azure AD e as respetivas funcionalidades?**

**R:** O Azure AD tem mais de 2600 aplicações previamente integradas da Microsoft, de fornecedores de serviços de aplicações ou de parceiros. Todas as aplicações previamente integradas suportam o SSO. O SSO permite-lhe utilizar as credenciais organizacionais para aceder às suas aplicações. Algumas das aplicações também suportam o aprovisionamento e cancelamento de aprovisionamento automatizados.

Para obter a lista completa das aplicações previamente integradas, consulte [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**P: E se a aplicação de que preciso não fizer parte do Azure AD Marketplace?**

**R:** Com o Azure AD Premium, pode adicionar e configurar qualquer aplicação que desejar. Consoante as funcionalidades da aplicação e das suas preferências, pode configurar o SSO e o aprovisionamento automatizado.  

Para obter mais detalhes, consulte:

* [Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory](active-directory-saas-custom-apps.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](active-directory-scim-provisioning.md) 

- - -
**P: Como os utilizadores iniciam sessão em aplicações com o Azure Active Directory?**

**R:** O Azure Active Directory oferece aos utilizadores várias formas de visualizarem e acederem às respetivas aplicações, tais como:

* O painel de acesso do Azure AD
* O iniciador de aplicações do Office 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Para mais informações, consulte [Implementar aplicações integradas ao Azure AD para os utilizadores](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**P: Quais são as várias formas de autenticação e início de sessão único do Azure Active Directory das aplicações?**

**R:** O Azure Active Directory suporta vários protocolos normalizados para a autenticação e autorização, tais como SAML 2.0, OpenID Connect, OAuth 2.0 e WS-Federation. O Azure AD também suporta cofres de palavras-passe e as capacidades de início de sessão automatizado para aplicações que apenas suportam autenticação baseada em formulários.  

Para obter mais informações, consulte:

* [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)
* [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Como funciona um início de sessão único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**P: Posso adicionar as aplicações que estiver a executar no local?**

**R:** O Proxy da Aplicação do Azure AD fornece um acesso fácil e seguro às Web Apps no local que escolher. Pode aceder a estas aplicações da mesma forma a que acede às aplicações SaaS no Azure Active Directory. Não é necessário nenhuma VPN ou alterar a infraestrutura de rede.  

Para mais detalhes, consulte [Como fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md).

- - -
**P: Como exigir a MFA para os utilizadores que acedem a uma determinada aplicação?**

**R:** Com o acesso condicional do Azure AD, pode atribuir uma política de acesso único a cada aplicação. Na sua política, pode exigir a MFA a qualquer momento ou quando os utilizadores não estiverem ligados à rede local.  

Para mais detalhes, consulte [Proteger o acesso ao Office 365 e a outras aplicações ligadas ao Azure Active Directory](active-directory-conditional-access.md).

- - -
**P: O que é o Aprovisionamento Automatizado do Utilizador para Aplicações SaaS?**

**R:** O Azure Active Directory permite-lhe automatizar a criação, a manutenção e a remoção de identidades de utilizadores em diversas aplicações (SaaS) na nuvem populares. 

Para obter mais informações, consulte[Automatizar o Aprovisionamento do Utilizador e Cancelar o Aprovisionamento a Aplicações SaaS com o Azure Active Directory ](active-directory-saas-app-provisioning.md)

- - -
<!--HONumber=Sep16_HO3-->


