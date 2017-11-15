---
title: "Orientações para programadores do acesso condicional do Azure Active Directory | Microsoft Docs"
description: "Orientações para programadores e cenários de acesso condicional do Azure AD"
services: active-directory
keywords: 
author: danieldobalian
manager: mbaldwin
editor: PatAltimore
ms.author: dadobali
ms.date: 07/19/2017
ms.assetid: 115bdab2-e1fd-4403-ac15-d4195e24ac95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: eddc1988e094a50ba7e41331a576846aa26f77a4
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Orientações para programadores do acesso condicional do Azure Active Directory

Azure Active Directory (AD) oferece várias formas de proteger a sua aplicação e proteger um serviço.  Uma destas funcionalidades exclusivo é o acesso condicional.  Acesso condicional permite que os programadores e os clientes empresariais para proteger serviços em diversos formas incluindo:

* Multi-Factor Authentication
* Permitir que apenas o Intune inscritos para aceder a serviços específicos
* Restringir as localizações de utilizador e IP intervalos

Para obter mais informações sobre as funcionalidades completas do acesso condicional, consulte [acesso condicional no portal clássico do Azure](../active-directory-conditional-access-azure-portal.md). 

Neste artigo, iremos focar-se no significa que o acesso condicional aos programadores criar aplicações do Azure AD.  Pressupõe que os dados de conhecimento de [único](active-directory-integrating-applications.md) e [multi-inquilino](active-directory-devhowto-multi-tenant-overview.md) aplicações e [padrões comuns da autenticação](active-directory-authentication-scenarios.md).

Iremos irá aprofundar o impacto de aceder a recursos que não têm controlo sobre que poderão ter políticas de acesso condicional são aplicadas.  Além disso, vamos explorar as implicações de acesso condicional em fluxo em-nome-de, aplicações web, aceder ao Microsoft Graph e chamar APIs.

## <a name="how-does-conditional-access-impact-an-app"></a>Como é que o acesso condicional afeta uma aplicação?

### <a name="app-types-impacted"></a>Tipos de aplicação afetados

Em casos mais comuns, o acesso condicional não alterar o comportamento de uma aplicação ou necessita de alterações do programador.  Apenas em determinados casos quando uma aplicação, indiretamente ou silencioso solicita um token para um serviço, uma aplicação necessita de alterações de código para processar o acesso condicional "desafios".  Poderá ser tão simple como executar um pedido de início de sessão interativo. 

Especificamente, os cenários seguintes necessitam de código para processar o acesso condicional "desafios": 

* Aplicações aceder à Microsoft Graph
* Aplicações a executar o fluxo em-nome-de
* Aplicações aceder aos vários serviços/recursos
* Aplicações de página única utilizando ADAL.js
* Web Apps chamar um recurso

Acesso condicional políticas podem ser aplicadas à aplicação, mas também podem ser aplicadas a uma API web acede a sua aplicação. Para obter mais informações sobre como configurar uma política de acesso condicional, consulte [introdução ao Azure Active Directory condicional acesso](../active-directory-conditional-access-azure-portal-get-started.md).

Dependendo do cenário, um cliente enterprise pode aplicar e remover as políticas de acesso condicional em qualquer altura.  Para a sua aplicação continuar a funcionar quando uma nova política é aplicada, é necessário implementar o processamento de "desafio". Os exemplos seguintes mostram o processamento de desafio. 

### <a name="conditional-access-examples"></a>Exemplos de acesso condicional

Alguns cenários requerem alterações de código para lidar com acesso condicional, enquanto que outras pessoas funcionam como é.  Seguem-se alguns cenários de utilizar o acesso condicional para efetuar a autenticação multifator que fornece algumas informações sobre a diferença.

* Está a criar uma aplicação iOS de inquilino único e aplicar uma política de acesso condicional.  A aplicação inicia sessão um utilizador e não pedir acesso a uma API.  Quando o utilizador inicia sessão, a política é invocada automaticamente e o utilizador precisa de realizar autenticação multifator (MFA). 
* Está a criar uma aplicação web do multi-inquilino que utiliza o Microsoft Graph para aceder ao Exchange, entre outros serviços.  Um cliente de enterprise que adopts esta aplicação define uma política de Exchange.  Quando a aplicação web solicita um token para MS Graph, a aplicação não será solicitada para estar em conformidade com a política.  O utilizador final está assinado com tokens válidos. Quando a aplicação tenta utilizar este token contra o Microsoft Graph para aceder a dados do Exchange, um desafio"afirmações" é devolvido na aplicação web através de ```WWW-Authenticate``` cabeçalho.  A aplicação, em seguida, pode utilizar o ```claims``` num pedido de novo, e o utilizador final será avisado para estar em conformidade com as condições. 
* Está a criar uma aplicação nativa que utiliza um serviço de camada média para aceder a uma API a jusante.  Um cliente de enterprise a empresa utilizar esta aplicação aplica-se uma política para a API a jusante.  Quando um utilizador final inicia sessão, a aplicação nativa solicita acesso para a camada média e envia o token.  A camada média efetua fluxo em-nome-de para pedir acesso para a API a jusante.  Neste momento, é apresentado um desafio"afirmações" à camada média. A camada média envia o desafio para a aplicação nativa, o que tem de estar em conformidade com a política de acesso condicional.

### <a name="complying-with-a-conditional-access-policy"></a>Complying com uma política de acesso condicional

Para várias topologias de aplicação diferente, uma política de acesso condicional é avaliada quando a sessão for estabelecida.  Como uma política de acesso condicional manipular a granularidade de aplicações e serviços, o ponto no qual é invocado depende descontos elevados no cenário que está a tentar realizar.

Quando a aplicação tenta aceder a um serviço com uma política de acesso condicional, poderá encontrar um desafio de acesso condicional.  Este desafio é codificado no `claims` parâmetro que é apresentada numa resposta do Azure AD ou o Microsoft Graph.  Eis um exemplo deste parâmetro desafio: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Os programadores podem demorar este desafio e o acrescentem para um novo pedido para o Azure AD.  Transmitir este estado pede ao utilizador final para efetuar qualquer ação necessária cumprir a política de acesso condicional. Nos seguintes cenários, são explicadas especificações o erro e como extrair o parâmetro. 

## <a name="scenarios"></a>Cenários

### <a name="prerequisites"></a>Pré-requisitos

Acesso condicional do Azure AD é uma funcionalidade incluída no [do Azure AD Premium](../active-directory-whatis.md#choose-an-edition).  Pode saber mais sobre o licenciamento de requisitos a [relatório de utilização não autorizada](../active-directory-conditional-access-unlicensed-usage-report.md).  Os programadores podem associar o [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), que inclui uma subscrição gratuita para o Enterprise Mobility Suite, que inclui o Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações aplicam-se nos seguintes cenários de acesso condicional:

* Aplicações aceder à Microsoft Graph
* Aplicações a executar o fluxo em-nome-de
* Aplicações aceder aos vários serviços/recursos
* Aplicações de página única utilizando ADAL.js

Nas secções seguintes, iremos irá colocar no comuns cenários em que são mais complexos.  As principais operativo princípio é acesso condicional são avaliadas as políticas no momento que o token é solicitado para o serviço que tenha uma política de acesso condicional aplicada, salvo se estiver a ser acedido através do Microsoft Graph.

## <a name="scenario-app-accessing-microsoft-graph"></a>Cenário: Aplicação aceder ao Microsoft Graph

Neste cenário, iremos guiá-o caso quando um acesso de pedidos de aplicação web para Microsoft Graph. A política de acesso condicional neste caso, foi possível atribuir o SharePoint, Exchange ou outro serviço que é acedido como uma carga de trabalho através do Microsoft Graph.  Neste exemplo, vamos supor que não há uma política de acesso condicional no Sharepoint Online.

![Aceder ao Microsoft Graph diagrama de fluxo de aplicações](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

A aplicação primeiro os pedidos de autorização para o Microsoft Graph que necessita de aceder a uma carga de trabalho a jusante sem acesso condicional.  O pedido seja bem-sucedido sem invocar qualquer política e a aplicação recebe tokens para o Microsoft Graph.  Neste momento, a aplicação pode utilizar o token de acesso num pedido de portador para o ponto final pedido. Agora, a aplicação precisa de aceder a um ponto final do Sharepoint Online da Microsoft Graph, por exemplo:`https://graph.microsoft.com/v1.0/me/mySite`

A aplicação já tem um token válido para o Microsoft Graph, pelo que pode efetuar o pedido de nova sem ser emitido um novo token. Este pedido falha e é emitido um desafio de afirmações do Microsoft Graph sob a forma de um erro HTTP 403 Proibido com um ```WWW-Authenticate``` desafio.
Eis um exemplo de resposta: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

O desafio de afirmações está dentro do ```WWW-Authenticate``` cabeçalho, o que pode ser analisado para extrair o parâmetro de afirmações para o pedido seguinte.  Uma vez que é acrescentado ao pedido de novo, sabe do Azure AD para avaliar a política de acesso condicional quando o utilizador de início de sessão e a aplicação está agora em conformidade com a política de acesso condicional.  Repetir o pedido para o ponto final do Sharepoint Online é concluída com êxito.

O ```WWW-Authenticate``` cabeçalho tem uma estrutura exclusiva e não é trivial analisar para extrair os valores.  Segue-se um método para ajudar a curto.

    ```C#
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
    ```

Para obter exemplos de código que demonstram como lidar com o desafio de afirmações, consulte o [em-nome-de código de exemplo](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) para ADAL .NET.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: Aplicação executar o fluxo em-nome-de

Neste cenário, iremos guiá-o caso em que uma aplicação nativa chama uma API web do serviço /.  Por sua vez, este serviço não [o "fluxo em-nome-de"](active-directory-authentication-scenarios.md#application-types-and-scenarios) para chamar um serviço a jusante.  No nosso caso, iremos tiver aplicado a nossa política de acesso condicional para o serviço a jusante (Web API 2) e estiver a utilizar uma aplicação nativa em vez de uma aplicação de servidor/daemon. 

![Aplicação efetuar o diagrama de fluxo em-nome-de](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

O pedido de token inicial para Web API 1 não solicitar o utilizador final para autenticação multifator conforme 1 de API Web não pode atingir sempre a API a jusante.  Depois de 1 de API Web tenta pedir um token em-nome-de utilizador para Web API 2, o pedido falha, uma vez que o utilizador não tem sessão iniciada com autenticação multifator.

Azure AD devolve uma resposta HTTP com alguns dados interessantes: 

> [!NOTE]
> Esta instância é uma descrição de erro de autenticação multifator, mas não existe uma vasta gama de `interaction_required` possíveis relativos ao acesso condicional.  

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

No nosso 1 de API Web, iremos catch o erro `error=interaction_required`e o envio de `claims` desafio para a aplicação de ambiente de trabalho.  Nessa altura, a aplicação de ambiente de trabalho pode tornar um novo `acquireToken()` chamar e acrescentar a `claims`desafio como um parâmetro de cadeia de consulta adicionais.  Este novo pedido exige que o utilizador efetue autenticação multifator e, em seguida, enviar este novo token de volta para Web API 1 e concluir o fluxo em-nome-de.

Para experimentar este cenário, consulte a nossa [exemplo de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  -Demonstra como passar o desafio de afirmações novamente a partir do Web API 1 para a aplicação nativa e construir um novo pedido de dentro da aplicação de cliente. 

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: Aplicação aceder aos vários serviços

Neste cenário, iremos guiá-o caso em que uma aplicação web acede dois serviços um deles tem uma política de acesso condicional atribuída.  Dependendo da lógica de aplicação, pode existir um caminho no qual a aplicação não necessita de acesso a ambos os serviços web.  Neste cenário, a ordem na qual pedir um token desempenha um papel importante para a experiência de utilizador final.

Vamos supor que temos o serviço web A e B e serviço web B tem a nossa política de acesso condicional aplicada.  Enquanto o pedido de autenticação interativa inicial requer consentimento para ambos os serviços, a política de acesso condicional não é necessário em todos os casos.  Se a aplicação pedir um token de serviço web B, em seguida, a política é invocada e os pedidos subsequentes para o serviço web A também for bem sucedida da seguinte forma.

![Diagrama de fluxo de vários serviços de acesso de aplicação](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

Em alternativa, se a aplicação inicialmente solicita um token para uma de serviço web, o utilizador final não será invocado a política de acesso condicional.  Isto permite que o Programador de aplicações controlar a experiência de utilizador final e não forçar a política de acesso condicional para ser invocado em todos os casos. As maiúsculas e minúsculas tricky é se a aplicação pede subsequentemente um token de serviço web B. Neste momento, o utilizador final tem de estar em conformidade com a política de acesso condicional.  Quando a aplicação tenta `acquireToken`, poderá gerar o seguinte erro (ilustrado no diagrama seguinte): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Aceder aos vários serviços pedir um novo token de aplicação](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Se a aplicação estiver a utilizar a biblioteca da ADAL, uma falha ao adquirir o token é sempre repetida interativamente.  Quando ocorre este pedido interativo, o utilizador final tem a oportunidade para estar em conformidade com o acesso condicional.  Isto é verdadeiro, a menos que o pedido é um `AcquireTokenSilentAsync` ou `PromptBehavior.Never` caso em que a aplicação precisa de realizar interativo ```AcquireToken``` pedido para dar a utilização de fim a oportunidade de estar em conformidade com a política. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Cenário: Única página aplicação (SPA) utilizando ADAL.js

Neste cenário, iremos guiá-o caso quando temos uma aplicação de página única (SPA), utilizando ADAL.js para chamar uma API web de acesso condicional protegido.  Esta é uma arquitetura de simple, mas tem alguns nuances que precisam de ser levados em consideração quando desenvolver à volta de acesso condicional.

ADAL.js, existem algumas funções que obter os tokens: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, e `acquireTokenRedirect(…)`. 

* `login()`obtém um token de ID através de um pedido de início de sessão interativo, mas não obter os tokens de acesso a qualquer serviço (incluindo uma API web de acesso condicional protegido).  
* `acquireToken(…)`em seguida, pode ser utilizado para obter automaticamente um token de acesso, o que significa que não mostrar a IU em qualquer circunstância.  
* `acquireTokenPopup(…)`e `acquireTokenRedirect(…)` são ambos utilizado para interativamente pedir um token para um recurso, o que significa que sempre mostrarem a IU de início de sessão.

Quando uma aplicação precisa de um token de acesso para chamar uma API Web, este tenta uma `acquireToken(…)`.  Se a sessão de token expirou ou é necessário para estar em conformidade com a política de acesso condicional, em seguida, a *acquireToken* falha de função e as utilizações de aplicação `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Aplicação de página única com o ADAL diagrama de fluxo](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Vamos guiá-lo através de um exemplo com o nosso cenário de acesso condicional.  O utilizador final apenas landed no site e não tem uma sessão.  Iremos efetuar um `login()` chamar, obter um ID de token sem multi-factor authentication.  Em seguida, o utilizador pedidos com êxito um botão que requer que a aplicação aos dados de pedido de uma API web.  A aplicação tenta efetuar um `acquireToken()` chamada, mas falha, uma vez que o utilizador não realizou ainda a autenticação multifator e tem de estar em conformidade com a política de acesso condicional.

Azure AD envia a resposta HTTP seguinte: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

A nossa aplicação precisa detetar o `error=interaction_required`.  Em seguida, pode utilizar a aplicação `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso.  O utilizador é forçado a fazer uma autenticação multifator. Depois do utilizador conclui o multi-factor authentication, a aplicação é emitida um token de acesso de raiz para o recurso pedido.

Para experimentar este cenário, consulte a nossa [exemplo de código em-nome-de JS SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Código de exemplo utiliza a política de acesso condicional e que registou anteriormente com uma SPA JS para demonstrar neste cenário de API web. Mostra como corretamente processar o desafio de afirmações e obter um token de acesso que pode ser utilizado para a API Web. Em alternativa, consulte geral [exemplo de código Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) para obter orientações sobre uma SPA angular para


## <a name="see-also"></a>Consultar também

* Para saber mais sobre as funcionalidades, consulte [acesso condicional no Azure AD](../active-directory-conditional-access-azure-portal.md).
* Para exemplos de código de mais do Azure AD, consulte [repositório do Github dos exemplos de código](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Para obter mais informações sobre do ADAL SDK e acesso a documentação de referência, consulte [guia biblioteca](active-directory-authentication-libraries.md).
* Para saber mais sobre os cenários de multi-inquilinos, consulte [como a sessão de utilizadores que utilizam o padrão de multi-inquilino](active-directory-devhowto-multi-tenant-overview.md).
