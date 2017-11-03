---
title: "O que é o acesso a aplicações e início de sessão no Azure Active Directory? | Microsoft Docs"
description: "Utilize o Azure Active Directory para ativar o início de sessão para todas as aplicações web e de SaaS que precisa para empresas."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 75d1a3fd-b3c5-4495-a5c8-c4c24145ff00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: 89bffc9726a2c54e59281045d16472335b2a7fed
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>O que é o acesso a aplicações e início de sessão no Azure Active Directory?
O início de sessão único significa que está a ser capazes de aceder a todas as aplicações e recursos que precisa para fins comerciais, ao iniciar sessão apenas depois de utilizar uma conta de utilizador único. Depois de iniciar sessão, pode aceder a todas as aplicações que precisar, sem ser necessário para autenticação (por exemplo, escreva uma palavra-passe) uma segunda vez.

Muitas organizações confiarem em software como um aplicações de serviço (SaaS), tais como o Office 365, a caixa e a Salesforce para a produtividade do utilizador final. Historicamente, tem de equipa de TI individualmente criar e atualizar as contas de utilizador em cada aplicação SaaS, e os utilizadores tenham de memorizar uma palavra-passe para cada aplicação SaaS.

Azure Active Directory expande do Active Directory no local para a nuvem, permitindo que os utilizadores utilizar a respetiva conta organizacional primária não só a iniciar sessão para os respetivos dispositivos associados a um domínio e recursos da empresa, mas também todos os web e aplicações SaaS necessário para o trabalho.

Por isso, não apenas os utilizadores não é necessário gerir vários conjuntos de nomes de utilizador e palavras-passe, as aplicações de acesso, pode ser automaticamente aprovisionado ou anular aprovisionado com base nos respetivos membros do grupo de organização e o respetivo estado como um empregados. Azure Active Directory apresenta acesso de segurança e controlos de governação que lhe permite gerir centralmente o acesso dos utilizadores em aplicações SaaS.

Azure AD permite a integração fácil para muitas das aplicações de SaaS populares hoje; fornece gestão de identidades e acessos e permite aos utilizadores de sessão único-para as aplicações diretamente, ou detetar e iniciá-los a partir de um portal como o Office 365 ou o painel de acesso do Azure AD.

A arquitetura da integração inclui os seguintes blocos modulares quatro principais:

* O início de sessão único permite aos utilizadores aceder às suas aplicações de SaaS com base na respetiva conta organizacional no Azure AD. O início de sessão único é o que permite aos utilizadores autenticar a uma aplicação utilizando a respetiva conta organizacional único.
* Aprovisionamento de utilizadores permite o aprovisionamento de utilizador e aprovisionamento automatizados para destino que saas com base nas alterações efetuadas no Windows Server Active Directory e/ou do Azure AD. Uma conta de aprovisionamento é o que permite que um utilizador autorizado a utilizar uma aplicação, depois de se tem a autenticação através de um início de sessão único.
* Gestão de acesso de aplicação centralizado no Portal de gestão do Azure permite acesso de aplicação de ponto único de SaaS e gestão, com a capacidade para delegar a efetuar de decisão de acesso de aplicação e aprovações a qualquer pessoa na organização
* Unified elaboração de relatórios e monitorização da atividade do utilizador no Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Como funciona um início de sessão único com o Azure Active Directory?
Quando um utilizador "iniciar sessão" a uma aplicação, avançam através de um processo de autenticação onde são necessárias para provarem que são que diga a estão. Sem início de sessão, isto é geralmente feito ao introduzir uma palavra-passe é armazenada na aplicação e o utilizador é necessário saber esta palavra-passe.

Azure AD suporta três formas diferentes de iniciar sessão para aplicações:

* **Federado Single Sign-On** permite que as aplicações redirecionar para o Azure AD para autenticação de utilizador em vez de pedir a sua própria palavra-passe. Isto é suportado para aplicações que suporte protocolos, tais como SAML 2.0, WS-Federation, ou o OpenID Connect e é o modo richest de início de sessão único.
* **Baseado em palavra-passe Single Sign-On** permite proteger o armazenamento de palavra-passe de aplicação e utilizar uma extensão de browser da web ou aplicação móvel de repetição. Isto melhora o início de sessão no processo existente fornecido pela aplicação, mas permite que um administrador gerir as palavras-passe e requer que o utilizador saber a palavra-passe.
* **Existente Single Sign-On** permite ao Azure AD tirar partido de qualquer existente-início de sessão único que foi configurado para a aplicação, mas permite que estas aplicações estar ligado ao portais de painel de acesso do Office 365 ou do Azure AD e também permite adicionais relatórios no Azure AD quando as aplicações são iniciadas não existe.

Depois de ter autenticar um utilizador com uma aplicação, também têm de ter um registo de conta aprovisionado a aplicação que indica a aplicação onde existe nível de acesso e permissões são dentro da aplicação. O aprovisionamento deste registo de conta ou pode ocorrer automaticamente, ou pode ocorrer manualmente por um administrador antes do utilizador é fornecido acesso de início de sessão único.

 Obter mais detalhes sobre estes modos de início de sessão único e o aprovisionamento abaixo.

### <a name="federated-single-sign-on"></a>Federado Single Sign-On
Federado Single Sign-On permite início de sessão permite que os utilizadores na sua organização a ser iniciada automaticamente uma aplicação SaaS de terceiros pelo Azure AD utilizando as informações de conta de utilizador do Azure AD.

Neste cenário, quando que já foram registados com o Azure AD e, se pretender aceder a recursos que são controlados por uma aplicação SaaS de terceiros, Federação elimina a necessidade de um utilizador ser novamente autenticados.

Azure AD pode suportar federado-início de sessão único com aplicações que suportam o 2.0 SAML, WS-Federation, ou o OpenID connect protocolos.

Consulte também: [gestão de certificados para federado o início de sessão único](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>Baseado em palavra-passe de início de sessão
Configuração baseada em palavra-passe de início de sessão permite que os utilizadores na sua organização a ser iniciada automaticamente uma aplicação SaaS de terceiros pelo Azure AD utilizando as informações de conta de utilizador da aplicação de SaaS de terceiros. Quando ativa esta funcionalidade, o Azure AD recolhe e armazena de forma segura as informações de conta de utilizador e a palavra-passe relacionada.

Do Azure AD pode suportar baseada em palavra-passe de início de sessão único para qualquer aplicação baseada na nuvem que tem uma baseado em HTML-página sessão. Ao utilizar um plug-in de browser personalizados, AAD automatiza o processo através de forma segura a obter as credenciais de aplicação, tais como o nome de utilizador e a palavra-passe do diretório de início de sessão do utilizador e introduz estas credenciais na página em nome do utilizador de início de sessão da aplicação . Existem dois casos de utilização:

1. **Administrador gere credenciais** – os administradores podem criar e gerir credenciais de aplicação e atribuir essas credenciais aos utilizadores ou grupos que necessitam de aceder à aplicação. Nestes casos, o utilizador final não precisa de saber as credenciais, mas ainda obtiver acesso de início de sessão único para a aplicação simplesmente clicando na mesma no respetivo painel de acesso ou através de uma ligação fornecida. Isto permite que ambos, gestão de ciclo de vida das credenciais pelo administrador, bem como conveniência para os utilizadores finais na qual não é necessário para memorizar ou faça a gestão de palavras-passe de aplicação específicos. As credenciais são ocultadas do utilizador final durante o início de sessão automatizado no processo; No entanto são tecnicamente Detetáveis pelo utilizador utilizando ferramentas de depuração do web e os utilizadores e os administradores devem seguir as mesmas políticas de segurança como se as credenciais foram apresentadas diretamente pelo utilizador. As credenciais de administrador fornecidos pelo são muito úteis quando fornecer acesso de conta que é partilhado entre vários utilizadores, tais como de redes sociais ou documento partilha de aplicações.
2. **Utilizador gere credenciais** – os administradores podem atribuir aplicações para os utilizadores finais e grupos e permitir que os utilizadores finais introduzir as suas próprias credenciais diretamente após o acesso à aplicação pela primeira vez no respetivo painel de acesso. Esta ação cria uma conveniência para os utilizadores finais na qual não é necessário introduzir continuamente as palavras-passe de específico da aplicação sempre que acederem a aplicação. Neste caso, utilize também pode ser utilizado como um stone stepping para modelos administrativos \ Gestão de credenciais, na qual o administrador pode definir novas credenciais para a aplicação numa data futura sem alterar a experiência de acesso de aplicações do utilizador final.

Em ambos os casos, as credenciais são armazenadas num estado encriptado no diretório, só são transmitidas através de HTTPS durante o processo de início de sessão automatizado. Utilizando o início de sessão único baseado em palavra-passe no, do Azure AD oferece uma solução de gestão de acesso de identidade conveniente para aplicações que não são capazes de suportar protocolos de Federação.

SSO baseada em palavra-passe baseia-se numa extensão de browser para obter as informações específicas da aplicação e o utilizador do Azure AD e aplicá-la para o serviço em segurança. Esta funcionalidade suporta a maioria das aplicações de SaaS de terceiros que são suportadas pelo Azure AD.

Para SSO baseada em palavra-passe, os browsers do utilizador final podem ser:
* Internet Explorer 8, 9, 10, 11 - no Windows 7 ou posterior
* Limite de aniversário da edição do Windows 10 ou posterior 
* Chrome – No Windows 7 ou posterior e no MacOS X ou posterior
* Firefox 26.0 ou posterior – no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

### <a name="existing-single-sign-on"></a>Existente Single Sign-On
Quando configurar o início de sessão para uma aplicação, o portal de gestão do Azure fornece uma terceira opção de "existente Single Sign-On". Esta opção permite simplesmente o administrador pode criar uma ligação para uma aplicação e coloque-a no painel de acesso para utilizadores selecionados.

Por exemplo, se existir uma aplicação que está configurada para autenticar utilizadores que utilizam 2.0 de serviços de Federação do Active Directory, um administrador pode utilizar a opção "existente Single Sign-On" para criar uma ligação ao mesmo no painel de acesso. Quando os utilizadores acedem a ligação, são autenticadas utilizando 2.0 de serviços de Federação do Active Directory ou qualquer único início de sessão solução existente é fornecida pela aplicação.

### <a name="user-provisioning"></a>Aprovisionamento de utilizadores
Para selecionadas aplicações, do Azure AD permite o aprovisionamento automatizado do utilizador e anular o aprovisionamento de contas em aplicações de SaaS de terceiros de dentro do Portal de gestão do Azure, utilizando as suas informações de identidade do Windows Server Active Directory ou do Azure AD. Quando um utilizador é concedido permissões no Azure AD para uma destas aplicações, uma conta pode ser criada automaticamente no destino da aplicação SaaS (aprovisionada).

Quando um utilizador for eliminado ou as respetivas informações de alterações no Azure AD, estas alterações também são refletidas na aplicação SaaS. Isto significa que, como configurar a gestão de ciclo de vida de identidade automática permite aos administradores controlar e fornecer o aprovisionamento e anular o aprovisionamento de aplicações SaaS. No Azure AD, esta automatização da gestão de ciclo de vida de identidades está ativada por aprovisionamento de utilizadores.

Para obter mais informações, consulte [aprovisionamento automatizado do utilizador e cancelar o aprovisionamento a aplicações SaaS](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Introdução à Galeria de aplicações do Azure AD
Pronto para começar? Para implementar o início de sessão entre o Azure AD e aplicações de SaaS que utiliza a sua organização, siga estas diretrizes.

### <a name="using-the-azure-ad-application-gallery"></a>Utilizar a Galeria de aplicações do Azure AD
O [Galeria de aplicações do Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) fornece uma lista de aplicações que se sabe suportarem um formulário de início de sessão no Azure Active Directory.

![][1]

Eis algumas sugestões para encontrar as aplicações pelas quais capacidades suportam:

* Azure AD suporta automática o aprovisionamento e cancelamento de aprovisionamento automatizados para todas as aplicações "Em destaque" no [Galeria de aplicações do Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/).
* Uma lista de aplicações federadas que suportam especificamente federada início de sessão único utilizando um protocolo, tais como SAML, WS-Federation, ou OpenID Connect pode ser encontrado [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Assim que tiver a encontrar a aplicação, pode começar a utilizar, siga as instruções passo a passo apresentadas na Galeria de aplicações e no portal de gestão do Azure para ativar o início de sessão único.

### <a name="application-not-in-the-gallery"></a>Aplicação não na galeria do?
Se a aplicação não se encontra na Galeria de aplicações do Azure AD, em seguida, tiver estas opções:

* **Adicionar uma aplicação não listada estiver a utilizar** -utilizar a categoria personalizada na Galeria de aplicações no portal de gestão do Azure para ligar uma aplicação não listada, que está a utilizar a sua organização. Pode adicionar qualquer aplicação que suporta SAML 2.0 como uma aplicação federada ou de qualquer aplicação que tenha uma baseado em HTML-página sessão como uma aplicação SSO de palavra-passe. Para obter mais detalhes, consulte este artigo no [adicionar a sua própria aplicação](application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Adicionar a sua própria aplicação estiver a desenvolver** - se de ter programado a aplicação por si, siga as diretrizes na documentação de programador do AD do Azure para implementar federado-início de sessão único ou aprovisionamento com o Azure AD graph API. Para obter mais informações, consulte estes recursos:
  
  * [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Pedir uma integração de aplicações** -pedir suporte para a aplicação tem de utilizar o [fórum de comentários do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-management-portal"></a>Utilizar o portal de gestão do Azure
Pode utilizar a extensão do Active Directory no Portal de gestão do Azure para configurar a aplicação-início de sessão único. Como primeiro passo, tem de selecionar um diretório na secção do Active Directory no portal:

![][2]

Para gerir as aplicações de SaaS de terceiros, pode mudar para o separador aplicações do diretório selecionado. Esta vista permite aos administradores:

* Adicionar novas aplicações a partir da galeria do Azure AD, bem como as aplicações que estiver a desenvolver
* Eliminar aplicações integradas
* Gerir as aplicações que já tiver integrado

As tarefas administrativas típicas para uma aplicação SaaS de terceiros são:

* Ativar o início de sessão com o Azure AD, utilizando a palavra-passe SSO ou, se disponível para o destino de SaaS, federadas SSO
* Opcionalmente, ativar aprovisionamento para o utilizador anular o aprovisionamento (gestão de ciclo de vida de identidade) e o aprovisionamento de utilizadores
* Para aplicações em que o aprovisionamento de utilizadores está ativado, selecionando os utilizadores que têm acesso a essa aplicação

Para aplicações de galeria que suportem federado-início de sessão único, configuração, normalmente, requer a fornecer definições de configuração adicionais, tais como certificados e metadados para criar uma confiança federada entre a aplicação de terceiros e o Azure AD. O Assistente de configuração orienta-os detalhes e fornece-lhe o facilitar o acesso aos dados específicos da aplicação SaaS e instruções.

Para aplicações de galeria que suportam o aprovisionamento de utilizadores automática, isto requer a dar permissões do Azure AD para gerir as contas na aplicação SaaS. No mínimo, tem de fornecer credenciais do Azure AD devem utilizar ao autenticar através da aplicação de destino. Indica se as definições de configuração adicionais tem de ser fornecido depende dos requisitos da aplicação.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Implementação do Azure AD integrado a aplicações para utilizadores
O Azure AD fornece várias formas personalizáveis para implementar aplicações para os utilizadores finais na sua organização:

* Painel de acesso do Azure AD
* Iniciador da aplicação do Office 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

O método que escolher para implementação na sua organização é o seu critério.

### <a name="azure-ad-access-panel"></a>Painel de acesso do Azure AD
Painel de acesso em https://myapps.microsoft.com é um portal baseado na web que permite que um utilizador final com uma conta organizacional no Azure Active Directory para ver e iniciar baseado na nuvem aplicações a que que tenham sido concedidos acesso pelo administrador do Azure AD . Se for um utilizador final com [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), também pode utilizar as capacidades de gestão de grupos self-service através do painel de acesso.

![][3]

O painel de acesso está separado do Portal de gestão do Azure e não exige que os utilizadores tenham uma subscrição do Azure ou uma subscrição do Office 365.

Para obter mais informações sobre o painel de acesso do Azure AD, consulte o [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Iniciador da aplicação do Office 365
Nas organizações que implementaram do Office 365, as aplicações foram atribuídas aos utilizadores através do Azure AD também serão apresentadas no portal do Office 365 em https://portal.office.com/myapps. Isto torna mais fácil e conveniente para os utilizadores numa organização para iniciar as suas aplicações sem ter de utilizar um portal segundo e é a solução iniciar aplicação recomendada para organizações com o Office 365.

![][4]

Para obter mais informações sobre o iniciador da aplicação do Office 365, consulte [ter a aplicação aparece no iniciador de aplicações do Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Início de sessão direto em aplicações federadas
Mais aplicações federadas que suportam SAML 2.0, WS-Federation ou OpenID connect também suporte a capacidade dos utilizadores iniciar a aplicação e, em seguida, obter sessão através do Azure AD através do redirecionamento automático ou clicar numa ligação para iniciar sessão. Isto é conhecido como fornecedor de serviços-iniciada no início de sessão e mais aplicações federadas na Galeria de aplicações do Azure AD suportam esta (consulte a documentação ligações a partir do Assistente de configuração de início de sessão único da aplicação no portal de gestão do Azure para obter detalhes).

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Ligações de início de sessão diretas para aplicações federadas, com base em palavra-passe ou existentes
Azure AD também suporta único início de sessão hiperligações diretas para aplicações individuais que suportam baseada em palavra-passe-início de sessão único, existente-início de sessão único e qualquer outra forma de federado-início de sessão único.

Estas ligações são URLs especificamente crafted que enviam um utilizador através do início de sessão do Azure AD no processo para uma aplicação específica, sem necessidade do utilizador iniciá-los a partir do painel de acesso do Azure AD ou do Office 365. Estes URLs de início de sessão único pode ser encontrados no separador Dashboard de qualquer aplicação previamente integrada na secção do Active Directory do portal de gestão do Azure, conforme mostrado na captura de ecrã abaixo.

![][6]

Estas ligações podem ser copiadas e coladas em qualquer local que pretende disponibilizar uma ligação de início de sessão para a aplicação selecionada. Isto pode ser num e-mail, ou em qualquer portal baseado na web personalizado que configurou para o acesso de aplicação do utilizador. Eis um exemplo de um Azure AD direto único início de sessão no URL para Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Semelhante para os URLs de organização específicos para o painel de acesso, pode ainda mais personalizar este URL, adicionando um dos domínios verificados ou Active Directory para o seu diretório depois do domínio myapps.microsoft.com. Isto garante que qualquer corporativa é carregada imediatamente a página de início de sessão sem o necessidade de introduzir primeiro o seu ID de utilizador do utilizador:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando um utilizador autorizado clica destas ligações específicas da aplicação, estes primeiro consulte os respetivos organizacional-página sessão (partindo do princípio de que não são assinados) e depois de início de sessão são redirecionados para a respetiva aplicação sem parar no painel de acesso, primeiro. Se o utilizador está em falta na pré-requisitos para aceder à aplicação, tais como a extensão de browser de início de sessão único baseado em palavra-passe, a ligação pedirá ao utilizador para instalar a extensão em falta. O URL de ligação também permanece constante se a configuração único início de sessão para a aplicação for alterada.

Estas ligações utilizam os mesmos mecanismos de controlo de acesso, como o painel de acesso e o Office 365, e apenas esses utilizadores ou grupos que tenham sido atribuídos a aplicação no portal de gestão do Azure conseguirá autenticar com êxito. No entanto, a qualquer utilizador que não está autorizada verá uma mensagem explicar o que não tenha sido concedidos acesso e recebem uma hiperligação para o painel de acesso para ver as aplicações disponíveis para os quais têm acesso de carga.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Localizar não sancionadas aplicações em nuvem com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
* [Introdução à gestão de acesso a aplicações](active-directory-managing-access-to-apps.md)
* [Capacidades de comparação para a gestão de identidades externas no Azure AD](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png
