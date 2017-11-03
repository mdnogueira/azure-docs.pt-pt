---
title: "Como fornecer acesso remoto seguro a aplicações no local"
description: "Aborda como utilizar o Proxy de aplicações do Azure AD para fornecer acesso remoto seguro às suas aplicações no local."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 426056d394af0a9ded28202615cb80c7b50e59fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Como fornecer acesso remoto seguro a aplicações no local

Os empregados querem hoje sejam produtivos em qualquer lugar em qualquer altura e a partir de qualquer dispositivo. Pretende trabalhar nos respetivos dispositivos, quer sejam tablets, telemóveis ou computadores portáteis. E esperam ser capazes de aceder às suas aplicações, ambas as aplicações de SaaS na nuvem e aplicações empresariais no local. Fornecer acesso a aplicações no local tem tradicionalmente envolvidos redes privadas virtuais (VPNs) ou zonas desmilitarizada (DMZ). Não só são estas soluções complexa e difícil de efetuar segura, mas são dispendiosas configurar e gerir.

Há uma melhor forma de!

Uma força de trabalho moderna na primeira-mobile, world primeiro de nuvem necessita de uma solução de moderna de acesso remoto. Proxy de aplicações do Azure AD é uma funcionalidade do Azure Active Directory oferece acesso remoto como um serviço. Isto significa que é fácil de implementar, utilizar e gerir.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>O que é o Proxy de aplicações do Azure Active Directory?
Proxy de aplicações do Azure AD fornece início de sessão único (SSO) e acesso remoto seguro para aplicações web alojadas no local. Algumas aplicações que pretende publicar incluem sites SharePoint, Outlook Web Access ou quaisquer outras LOB aplicações web que tem. Estas aplicações de web no local estão integradas com o Azure AD, a plataforma de controlo que é utilizada pelo Office 365 e a mesma identidade. Os utilizadores finais podem aceder as aplicações locais da mesma forma que acedem a O365 e outras aplicações de SaaS integradas com o Azure AD. Não precisa de alterar a infraestrutura de rede ou necessitar de VPN fornecer esta solução para os seus utilizadores.

## <a name="why-is-application-proxy-a-better-solution"></a>Porque é que o Proxy de aplicações a melhor solução?
Proxy de aplicações do Azure AD fornece uma solução de acesso remoto simples, segura e económica para todas as suas aplicações no local.

Proxy de aplicações do Azure AD é:

* **Simples**
   * Não precisa de alterar ou atualizar as suas aplicações para trabalhar com o Proxy de aplicações. 
   * Os utilizadores obtêm uma experiência de autenticação consistente. Podem utilizar o portal de MyApps para obter início de sessão único para ambas as aplicações de SaaS na nuvem e de aplicações no local. 
* **Proteger**
   * Quando publica as suas aplicações com o Proxy de aplicações do Azure AD, pode tirar partido dos controlos de autorização avançado e análises de segurança no Azure. Obter a segurança de escala da nuvem e funcionalidades de segurança do Azure como a verificação de dois passos e de acesso condicional.
   * Não tem de abrir as ligações de entrada através da sua firewall para conceder aos seus utilizadores acesso remoto. 
* **Rentável**
   * Proxy de aplicações funciona na nuvem, pelo que pode poupar tempo e dinheiro. Soluções no local, normalmente, tem de configurar e manter o DMZ, servidores edge ou outras infraestruturas complexas.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Que tipo de trabalho de aplicações com o Proxy de aplicações?
Com o Proxy de aplicações do Azure AD pode aceder a diferentes tipos de aplicações internas:

* Aplicações Web que utilizem [autenticação integrada do Windows](active-directory-application-proxy-sso-using-kcd.md) para autenticação  
* As aplicações que utilizam baseada em formulários Web ou [com base no cabeçalho](application-proxy-ping-access.md) acesso  
* Web APIs que pretende expor avançado aplicações em dispositivos diferentes  
* As aplicações alojadas por trás de um [Gateway de ambiente de trabalho remoto](application-proxy-publish-remote-desktop.md)  
* Aplicações de cliente avançada que estão integradas com Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Como funciona o Proxy de aplicações?
Existem dois componentes que terá de configurar para tornar o Proxy de aplicações de trabalho: um conector e um ponto final externo. 

O conector é um agente simples que se encontre num servidor Windows dentro da sua rede. O conector facilita o fluxo de tráfego do serviço de Proxy de aplicações na nuvem para a sua aplicação no local. Utiliza apenas ligações de saída, pelo que não tem de abrir as portas de entrada ou colocar qualquer coisa na rede de Perímetro. Os conectores são sem monitorização de estado e solicitar informações a partir da nuvem conforme necessário. Para obter mais informações sobre conectores, como como estes balanceamento de carga e autenticar, consulte [conetores da Proxy da aplicação Azure compreender AD](application-proxy-understand-connectors.md). 

O ponto final externo é a forma como os seus utilizadores atingir as suas aplicações enquanto fora da rede. Quer podem aceder diretamente a um URL externo que determinar ou podem aceder à aplicação através do portal de MyApps. Quando os utilizadores aceder a um destes pontos finais, podem autenticar no Azure AD e, em seguida, são encaminhados através do conector para a aplicação no local.

 ![Diagrama de Proxy de aplicações AzureAD](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. O utilizador acede a aplicação através do serviço de Proxy de aplicações e é direcionado para a página de início de sessão do Azure AD para a autenticação.
2. Após um bem-sucedida início de sessão, um token é gerado e enviado para o dispositivo cliente.
3. O cliente envia o token para o serviço de Proxy de aplicações, que obtém o nome principal de utilizador (UPN) e o nome principal de segurança (SPN) a partir do token, em seguida, redireciona o pedido para o conector do Proxy de aplicações.
4. Se tiver configurado o início de sessão único, o conector efetua qualquer autenticação adicional necessária em nome do utilizador.
5. O conector envia o pedido para a aplicação no local.  
6. A resposta é enviada através do serviço Proxy de aplicações e do conector para o utilizador.

### <a name="single-sign-on"></a>Início de sessão único
Proxy de aplicações do Azure AD fornece início de sessão único (SSO) para as aplicações que utilizam a autenticação integrada de Windows (IWA) ou aplicações com suporte para afirmações. Se a sua aplicação utilizar o IWA, Proxy de aplicações representa o utilizador tem a utilização de delegação restrita de Kerberos para fornecer SSO. Se tiver uma aplicação com suporte para afirmações confianças do Active Directory do Azure, o SSO funciona porque o utilizador já foi autenticado pelo Azure AD.

Para mais informações sobre o Kerberos, consulte [todas quiser saber sobre o delegação de restrita de Kerberos (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Gerir aplicações
Assim que a aplicação publicada com o Proxy da aplicação, pode geri-lo como qualquer outra aplicação de empresa no portal do Azure. Pode utilizar funcionalidades de segurança do Azure Active Directory como a verificação de dois passos e de acesso condicional, controlar permissões de utilizador e personalizar a imagem corporativa da sua aplicação. 

## <a name="get-started"></a>Introdução

Antes de configurar o Proxy de aplicações, certifique-se de que tem um suportados [edição do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) e um diretório do Azure AD para o qual é um administrador global.

Introdução ao Proxy de aplicações em dois passos:

1. [Ativar o Proxy da aplicação e configurar o conector](active-directory-application-proxy-enable.md).    
2. [Publicar aplicações](active-directory-application-proxy-publish.md) -utilize o assistente rápido e fácil para obter as suas aplicações no local publicado e está acessível remotamente.

## <a name="whats-next"></a>Passos seguintes?
Depois de publicar a sua primeira aplicação, não há muito mais pode fazer com o Proxy de aplicações:

* [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
* [Publicar aplicações com o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
* [Saiba mais sobre os conectores de Proxy de aplicações do Azure AD](application-proxy-understand-connectors.md)
* [Trabalhar com servidores de Proxy no local existentes](application-proxy-working-with-proxy-servers.md) 
* [Definir uma página inicial personalizada](application-proxy-office365-app-launcher.md)

Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](http://blogs.technet.com/b/applicationproxyblog/)

