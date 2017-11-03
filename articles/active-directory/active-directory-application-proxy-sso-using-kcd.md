---
title: "Início de sessão único com o Proxy de aplicações | Microsoft Docs"
description: "Aborda como fornecer início de sessão com o Proxy de aplicações do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 149af1f68e574f78127a9c2de8a0e79ed8774d29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Delegação restrita de Kerberos para o início de sessão único às suas aplicações com o Proxy da aplicação

Pode fornecer o início de sessão único para on-premises as aplicações publicadas através do Proxy de aplicações que estão protegidos com a autenticação integrada do Windows. Estas aplicações necessitam de uma permissão Kerberos para o acesso. Proxy de aplicações utiliza a delegação restrita de Kerberos (KCD) para suportar estas aplicações. 

Pode ativar o início de sessão único às suas aplicações utilizando a autenticação integrada de Windows (IWA), concedendo permissão de conectores do Proxy de aplicações no Active Directory para representar os utilizadores. Os conectores de utilizam esta permissão para enviar e receber tokens em nome daqueles.

## <a name="how-single-sign-on-with-kcd-works"></a>Como-início de sessão único com KCD funciona
Este diagrama explica o fluxo quando um utilizador tenta aceder a uma aplicação no local que utiliza IWA.

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. O utilizador introduz o URL para aceder à aplicação no local através do Proxy de aplicações.
2. Proxy da aplicação redireciona o pedido para serviços de autenticação do Azure AD para preauthenticate. Neste momento, aplica-se do Azure AD qualquer autenticação aplicável e políticas de autorização, como a autenticação multifator. Se o utilizador é validado, do Azure AD cria um token e envia-a para o utilizador.
3. O utilizador transmite o token para o Proxy de aplicações.
4. Proxy de aplicações valida o token e obtém o nome Principal de utilizador (UPN) do mesmo e, em seguida, envia o pedido, o UPN e o nome do Principal do serviço (SPN) para o conector através de um canal seguro dually autenticado.
5. O conector efetua a negociação de delegação restrita de Kerberos (KCD) com o local AD, representar o utilizador para obter um token de Kerberos para a aplicação.
6. Do Active Directory envia o token de Kerberos para a aplicação para o conector.
7. O conector envia o pedido original para o servidor de aplicações, utilizando o token Kerberos que recebeu do AD.
8. A aplicação envia a resposta para o conector, que, em seguida, é devolvido para o serviço de Proxy de aplicações e, finalmente, para o utilizador.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar com o início de sessão único para aplicações de IWA, certifique-se de que o ambiente está preparado com as seguintes definições e as configurações:

* As suas aplicações, como aplicações Web do SharePoint, são definidas para utilizar a autenticação integrada do Windows. Para obter mais informações, consulte [ativar o suporte para a autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou para SharePoint, consulte [planear a autenticação do Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Todas as suas aplicações têm [nomes principais de serviço](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* O servidor que executa o conector e o servidor que executa a aplicação estão associados a um domínio e parte do mesmo domínio ou domínios fidedignos. Para obter mais informações sobre a associação a um domínio, consulte [associar um computador a um domínio](https://technet.microsoft.com/library/dd807102.aspx).
* O servidor que executa o conector tem acesso para ler o atributo TokenGroupsGlobalAndUniversal para os utilizadores. Esta predefinição poderá ter sido afetada por proteger o ambiente de segurança.

### <a name="configure-active-directory"></a>Configurar o Active Directory
A configuração do Active Directory varia, dependendo se o conector do Proxy de aplicações e o servidor de aplicações se encontram no mesmo domínio ou não.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Conector e o servidor de aplicações no mesmo domínio
1. No Active Directory, aceda a **ferramentas** > **utilizadores e computadores**.
2. Selecione o servidor que executa o conector.
3. Clique com botão direito e selecione **propriedades** > **delegação**.
4. Selecione **confiar no computador para delegação apenas para serviços especificados**. 
5. Em **serviços a que esta conta pode apresentar credenciais delegadas** adicionar o valor para a identidade SPN do servidor de aplicações. Isto permite que o conector do Proxy da aplicação representar os utilizadores no AD em relação as aplicações definidas na lista.

   ![Captura de ecrã de janela de propriedades do conector SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Conector e o servidor de aplicações em domínios diferentes
1. Para obter uma lista de pré-requisitos para trabalhar com o KCD entre domínios, consulte [delegação restrita de Kerberos entre domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. Utilize o `principalsallowedtodelegateto` propriedade no servidor do conector para ativar o Proxy de aplicações delegar para o servidor do conector. O servidor de aplicações é `sharepointserviceaccount` e o servidor delegating `connectormachineaccount`. Para o Windows 2012 R2, utilize este código como um exemplo:

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Sharepointserviceaccount pode ser a conta da máquina SPS ou uma conta de serviço sob a qual o conjunto de aplicação SP está em execução.

## <a name="configure-single-sign-on"></a>Configurar o início de sessão único 
1. Publicar a aplicação, de acordo com as instruções descritas em [publicar aplicações com o Proxy de aplicações](application-proxy-publish-azure-portal.md). Certifique-se de que seleciona **do Azure Active Directory** como o **método de pré-autenticação**.
2. Após a aplicação é apresentada na lista de aplicações da empresa, selecione-o e clique em **de sessão único-**.
3. Defina o modo de início de sessão único para **autenticação integrada do Windows**.  
4. Introduza o **SPN de aplicação interna** do servidor de aplicações. Neste exemplo, o SPN para a nossa aplicação publicada é http/www.contoso.com. Este SPN tem de estar na lista de serviços a que o conector pode apresentar credenciais delegadas. 
5. Escolha o **identidade delegada de início de sessão** para o conector para utilizar em nome dos seus utilizadores. Para obter mais informações, consulte [trabalhar com diferentes no local e identidades de nuvem](#Working-with-different-on-premises-and-cloud-identities)

   ![Configuração de aplicações avançadas](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO para aplicações não Windows
O fluxo de delegação de Kerberos no Proxy de aplicações do Azure AD começa quando o Azure AD autentica o utilizador na nuvem. Depois do pedido chega no local, o conector do Proxy de aplicações do Azure AD emite uma permissão de Kerberos em nome do utilizador através da interação com o Active Directory local. Este processo é referido como o delegação de restrita de Kerberos (KCD). A fase seguinte, é enviado um pedido para a aplicação de back-end com esta permissão de Kerberos. Existem vários protocolos que definem como enviar esses pedidos. A maioria dos servidores de Windows não esperam negociar/SPNego é agora suportada no Proxy de aplicações do Azure AD.

Para mais informações sobre o Kerberos, consulte [todas quiser saber sobre o delegação de restrita de Kerberos (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Aplicações do Windows não normalmente nomes de utilizador do utilizador ou nomes de conta do SAM em vez do domínio endereços de correio eletrónico. Se essa situação aplica-se às suas aplicações, terá de configurar o campo de identidade do delegado de início de sessão para ligar as identidades de nuvem para as identidades de aplicação. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Trabalhar com diferentes no local e identidades de nuvem
Proxy de aplicações parte do princípio de que os utilizadores têm exatamente a mesma identidade na nuvem e no local. Se não for esse o caso, pode pode ainda utilizar KCD para o início de sessão único. Configurar um **delegado a identidade de início de sessão** para cada aplicação especificar que identidade deve ser utilizada ao efetuar o início de sessão único.  

Esta capacidade permite muitas organizações que tenham diferentes no local e identidades de nuvem para terem SSO da nuvem para aplicações no local, sem que os utilizadores introduzam diferentes nomes de utilizador e palavras-passe. Isto inclui as organizações que:

* Tem vários domínios internamente (joe@us.contoso.com, joe@eu.contoso.com) e um único domínio na nuvem (joe@contoso.com).
* Possui internamente o nome de domínio não encaminháveis internos (joe@contoso.usa) e um tipo legal na nuvem.
* Não utilize nomes de domínio internamente (joe)
* Utilize aliases diferentes no local e na nuvem. Por exemplo, joe-johns@contoso.com vs.joej@contoso.com  

Com o Proxy de aplicações, pode selecionar qual identidade a utilizar para obter a permissão de Kerberos. Esta definição é por aplicação. Algumas destas opções são adequadas para os sistemas que não aceite o formato do endereço de correio eletrónico, outros são concebidos para início de sessão alternativo.

![Captura de ecrã do início de sessão delegado identidade parâmetro](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Se for utilizada a identidade do delegado de início de sessão, o valor pode não ser exclusivo em todos os domínios ou florestas na sua organização. Pode evitar este problema através da publicação estas duas vezes com dois diferentes grupos de conector de aplicações. Uma vez que cada aplicação tem um público-alvo de utilizador diferente, pode associar os conectores para um domínio diferente.

### <a name="configure-sso-for-different-identities"></a>Configurar o SSO para diferentes identidades
1. Configure definições do Azure AD Connect para a identidade principal é o endereço de e-mail (mail). Isto é feito como parte do processo de personalizar, alterando o **Nome Principal de utilizador** campo nas definições de sincronização. Estas definições determinam também a forma como os utilizadores iniciar sessão no Office 365, Windows10 dispositivos e outras aplicações que utilizam o Azure AD como o respetivo arquivo de identidade.  
   ![Identificar utilizadores captura de ecrã - pendente de nome Principal de utilizador](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nas definições de configuração da aplicação para a aplicação que pretende modificar, selecione o **identidade delegada de início de sessão** a utilizar:

   * Nome Principal de utilizador (por exemplo, joe@contoso.com)
   * Nome Principal de utilizador de alternativa (por exemplo, joed@contoso.local)
   * Parte de nome de utilizador do nome do Principal de utilizador (por exemplo, João)
   * Parte do nome de utilizador de nome Principal de utilizador alternativo (por exemplo, joed)
   * Nome da conta do SAM no local (depende a configuração do controlador de domínio)

### <a name="troubleshooting-sso-for-different-identities"></a>Resolução de problemas SSO para diferentes identidades
Se existir um erro no processo de SSO, é apresentado no registo de eventos do conector máquina conforme explicado no [resolução de problemas](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
No entanto, em alguns casos, o pedido é enviado com êxito para a aplicação de back-end enquanto esta aplicação responde em diversas outras respostas HTTP. Nestes casos de resolução de problemas, deve começar, examinando o número de evento 24029 na máquina de conector no registo de eventos de sessão do Proxy de aplicações. A identidade do utilizador que foi utilizada para delegação é apresentado no campo "utilizador" dentro os detalhes do evento. Para ativar o registo da sessão, selecione **Mostrar análise e os registos de depuração** no menu de vista do Visualizador de eventos.

## <a name="next-steps"></a>Passos seguintes

* [Como configurar uma aplicação de Proxy de aplicações a utilizar a delegação restrita de Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Resolver problemas com o Proxy da aplicação](active-directory-application-proxy-troubleshoot.md)


Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](http://blogs.technet.com/b/applicationproxyblog/)

