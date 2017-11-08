---
title: Como obter AppSource certificada para Azure Active Directory | Microsoft Docs
description: "Detalhes sobre como obter a sua aplicação AppSource certificada para Azure Active Directory."
services: active-directory
documentationcenter: 
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: ca5105ff8ec45e703a3fb8e0cbb8666eb0b2c38e
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Como obter AppSource certificada para Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) é um destino para os utilizadores empresariais, detetar, tente e gerir aplicações de SaaS de linha de negócio (autónomo SaaS e suplementares para produtos de Microsoft SaaS existentes).

Para listar aplicações de SaaS no AppSource autónoma, a aplicação tem de aceitar início de sessão único de contas de trabalho a partir de qualquer da empresa ou organização que tenha o Azure Active Directory. O processo de início de sessão tem de utilizar o [OpenID Connect](./active-directory-protocols-openid-connect-code.md) ou [OAuth 2.0](./active-directory-protocols-oauth-code.md) protocolos. Não é aceite a integração de SAML para certificação AppSource.

## <a name="guides-and-code-samples"></a>Guias e exemplos de código
Se pretender saber mais sobre como integrar a sua aplicação no Azure Active Directory utilizando o ID de abrir ligar, siga os nossos guias e exemplos de código a [guia para programadores do Azure Active Directory](./active-directory-developers-guide.md#get-started "começar com o Azure AD para os programadores").

## <a name="multi-tenant-applications"></a>Aplicações de multi-inquilinos

Uma aplicação que aceite inícios de sessão dos utilizadores de qualquer empresa ou organização que tenham o Azure Active Directory sem necessidade de uma instância separada, a configuração ou a implementação é conhecida como um *aplicação multi-inquilino*. AppSource recomenda que as aplicações implementam vários inquilinos para ativar o *clique único* livre experiência de avaliação.

Para ativar vários inquilinos na sua aplicação:
- Definir `Multi-Tenanted` propriedade `Yes` nas informações do seu registo de aplicação do [Portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (por predefinição, as aplicações criadas no Portal do Azure estão configuradas como *deinquilinoúnico*)
- Atualizar o código para enviar pedidos para o '`common`' ponto final (atualizar o ponto final de *https://login.microsoftonline.com/ {yourtenant}* para *https://login.microsoftonline.com/common*)
- Em algumas plataformas, como o ASP.NET, também tem de atualizar o seu código para aceitar vários emissores

Para obter mais informações sobre vários inquilinos, consulte: [como iniciar sessão em qualquer utilizador do Azure Active Directory (AD) através do padrão de aplicação multi-inquilino](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Aplicações de inquilino único
As aplicações que aceitam apenas inícios de sessão dos utilizadores de uma instância do Azure Active Directory definido são conhecidas como *único inquilino aplicação*. Os utilizadores externos (incluindo as contas de empresa ou escola de outras organizações ou conta pessoal) podem iniciar sessão a uma aplicação de inquilino único após a adição de cada utilizador como *conta de convidado* para o Azure Active Directory instância que o aplicação está registada. Pode adicionar utilizadores como as contas de convidados para um Azure Active Directory através do [ *colaboração B2B do Azure AD* ](../active-directory-b2b-what-is-azure-ad-b2b.md) - e pode ser feita [x509securitytokenparameters](../active-directory-b2b-code-samples.md). Quando adiciona um utilizador como conta de convidado para um Azure Active Directory, é enviado um e-mail de convite para o utilizador, tem de aceitar o convite clicando na hiperligação no e-mail de convite. Convites para que são enviados para um utilizador adicional numa organização convidando que também seja membro da organização do parceiro não são necessários para aceitar um convite para iniciar sessão.

Aplicações de inquilino único podem ativar o *contactar-Me* experiência, mas se pretender ativar a experiência de avaliação gratuita / de clique único que recomenda AppSource, ativar vários inquilinos na sua aplicação em vez disso.


## <a name="appsource-trial-experiences"></a>AppSource experiências de avaliação

### <a name="free-trial-customer-led-trial-experience"></a>Avaliação gratuita (guiado de cliente experiência de avaliação) 
O *guiado de cliente a versão de avaliação* é a experiência que AppSource recomenda conforme oferece um acesso de clique único à sua aplicação. Abaixo uma ilustração de como esta experiência aspeto:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Utilizador localiza a aplicação no AppSource Web Site</li><li>Seleciona a opção 'Avaliação gratuita'</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource redireciona o utilizador para um URL no seu web site</li><li>Inicia o web site a <i>single-sign-on</i> processos automaticamente (no carregamento da página)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Utilizador é redirecionado para a página de início de sessão do Microsoft</li><li>Utilizador fornece credenciais para iniciar sessão</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Utilizador fornece o consentimento para a sua aplicação</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Início de sessão terminar e de utilizador é redirecionado novamente para o web site</li><li>Utilizador começa a versão de avaliação gratuita</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Contactar-Me (guiado de parceiro de experiência de avaliação)
O *experiência de avaliação gratuita do parceiro* podem ser utilizados quando uma manual ou uma operação de longa duração tem de acontecer aprovisionar o utilizador / da empresa: por exemplo, a aplicação tem de aprovisionar máquinas virtuais, operações ou instâncias de base de dados que demorar muito tempo a concluir. Neste caso, após o utilizador seleciona o *'Pedido avaliação'* botão e preenche a saída de um formulário, AppSource envia-lhe as informações de contacto do utilizador. Ao receber esta informação, em seguida, aprovisionar o ambiente e enviar as instruções para o utilizador sobre como aceder a experiência de avaliação:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Utilizador localiza a sua aplicação AppSource web site</li><li>Seleciona a opção 'Contactar-Me'</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Preenche fora de um formulário com as informações de contacto</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Recebe informações do utilizador</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Configurar o ambiente</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Utilizador contacto com as informações de avaliação</td>
        </tr>
        </table><br/><br/>
        <ul><li>Recebe informações do utilizador e a instância de avaliação do programa de configuração</li><li>Enviar a hiperligação para aceder à aplicação ao utilizador</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Utilizador acede a sua aplicação e concluir o processo de início de sessão único</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Utilizador fornece o consentimento para a sua aplicação</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Início de sessão terminar e de utilizador é redirecionado novamente para o web site</li><li>Utilizador começa a versão de avaliação gratuita</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Mais informações
Para obter mais informações sobre a experiência de avaliação AppSource, consulte [este vídeo](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre como criar aplicações que suportam o Azure Active Directory inícios de sessão, consulte [cenários de autenticação para o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Para obter informações sobre como listar a aplicação de SaaS na AppSource, visite consulte [AppSource informações do parceiro](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Obter Suporte
Para a integração do Azure Active Directory, utilizamos [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) com a Comunidade para fornecer suporte. 

É vivamente recomendado fazer as perguntas na Stack Overflow pela primeira vez e procurar problemas existentes para ver se alguém fez a sua pergunta antes. Certifique-se de que as suas questões ou comentários são etiquetados com [ `[azure-active-directory]` e `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->