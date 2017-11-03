---
title: "Como criar uma aplicação que pode iniciar sessão em qualquer utilizador do Azure AD"
description: "Passo a passo instruções para a criação de uma aplicação que podem iniciar sessão um utilizador de qualquer inquilino do Azure Active Directory, também conhecido como uma aplicação multi-inquilino."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: b409aa762b60c6bed0ee26f4b9fa7c347d9eb997
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Como iniciar sessão em qualquer utilizador do Azure Active Directory (AD) através do padrão de aplicação multi-inquilino
Se oferecer um Software como uma aplicação de serviço para muitas organizações, pode configurar a sua aplicação para aceitar inícios de sessão de qualquer inquilino do Azure AD.  Esta configuração denomina-no Azure AD, tornando o multi-inquilino de aplicação.  Os utilizadores em qualquer inquilino do Azure AD será possível iniciar sessão na sua aplicação após consenting para utilizar a respetiva conta com a sua aplicação.  

Se tiver uma aplicação existente que tem o seu próprio sistema conta ou suporta outros tipos de início de sessão a partir de outros fornecedores de nuvem, adicionar do Azure AD início de sessão a partir de nenhum inquilino é simple. Apenas registar a sua aplicação, adicione o código de início de sessão através de OAuth2, OpenID Connect ou SAML e colocar um botão de "Início de sessão In with Microsoft" na sua aplicação. Clique no botão seguinte para saber mais sobre a aplicação de imagem corporativa.

[![Botão Iniciar sessão][AAD-Sign-In]][AAD-App-Branding]

Este artigo pressupõe que já estiver familiarizado com a criação de uma aplicação de inquilino único para o Azure AD.  Se não estiver, head fazer uma cópia de segurança para o [home page de guia para programadores] [ AAD-Dev-Guide] e experimente um dos nossos inícios rápidos!

Existem quatro passos simples para converter a aplicação numa aplicação multi-inquilino do Azure AD:

1. Atualizar o registo da aplicação ser multi-inquilino
2. Atualize o código para enviar pedidos para o /common de ponto final 
3. Atualizar o código para processar vários valores de emissor
4. Compreender o consentimento do utilizador e administrador e fazer alterações ao código adequado

Vamos ver cada passo em detalhe. Também pode ir diretamente para [esta lista de amostras de multi-inquilinos][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registo ser multi-inquilino
Por predefinição, os registos de aplicações/API web no Azure AD são único inquilino.  Pode efetuar o registo de multi-inquilino ao encontrar o comutador "Múltiplos Tenanted" na página de propriedades de registo da aplicação no [portal do Azure] [ AZURE-portal] e defini-la como "Sim".

Tenha também em atenção, antes de uma aplicação pode ser efetuada a multi-inquilino, Azure AD requer o URI de ID de aplicação da aplicação a ser globalmente exclusivo. O URI de ID de aplicação é uma das formas que uma aplicação for identificada nas mensagens de protocolo.  Para uma aplicação de inquilino único, é suficiente para o URI de ID de aplicação a ser exclusivos dentro desse inquilino.  Para uma aplicação multi-inquilino, tem de ser globalmente exclusivo para que o Azure AD pode encontrar a aplicação em todos os inquilinos.  Exclusividade global é imposta, exigindo que o URI de ID de aplicação ter um nome de anfitrião que corresponde a um domínio verificado do inquilino do Azure AD.  Por exemplo, se o nome do seu inquilino era contoso.onmicrosoft.com, em seguida, um URI de ID de aplicação será `https://contoso.onmicrosoft.com/myapp`.  Se o seu inquilino tinha um domínio verificado do `contoso.com`, também deverá ser um URI de ID de aplicação válido `https://contoso.com/myapp`.  Se o URI de ID de aplicação não seguir este padrão, a definição de uma aplicação como falha de multi-inquilino.

Registos de cliente nativo são multi-inquilinos por predefinição.  Não precisa de efetuar qualquer ação para tornar um nativo multi-inquilino registo da aplicação de cliente.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualize o código para enviar pedidos para /common
Uma aplicação de inquilino único, pedidos de início de sessão são enviados para início de sessão no ponto final o inquilino. Por exemplo, para contoso.onmicrosoft.com o ponto final, seria:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Pedidos enviados para o ponto final de um inquilino podem iniciar sessão em utilizadores (ou convidados) nesse inquilino para aplicações nesse inquilino.  Com uma aplicação multi-inquilino, a aplicação não sabe adiantado que inquilino, o utilizador é de, pelo que não é possível enviar pedidos para o ponto final de um inquilino.  Em vez disso, os pedidos são enviados para um ponto final que multiplexes entre inquilinos todas as do Azure AD:

    https://login.microsoftonline.com/common

Quando o Azure AD recebe um pedido no /common ponto final, inicia o utilizador e como um consequence Deteta que o utilizador é a partir de inquilino.  O/ponto final comum funciona com todos os protocolos de autenticação suportados pelo Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 e WS-Federation.

A resposta de início de sessão para a aplicação, em seguida, contém um token que representa o utilizador.  O valor de emissor de token de indica que o utilizador é a partir de inquilino de uma aplicação.  Quando uma resposta devolve a partir de /common ponto final, o valor de emissor no token corresponde ao inquilino do utilizador.  É importante ter em conta o /common ponto final não é um inquilino e não é um emissor, é apenas um multiplexer.  Quando utilizar /common, tem de ser atualizado para efetuar isto em consideração a lógica da sua aplicação para validar os tokens. 

Conforme mencionado anteriormente, aplicações de multi-inquilinos também devem fornecer uma experiência de início de sessão consistente para utilizadores, seguir a diretrizes de imagem corporativa de aplicação do Azure AD. Clique no botão seguinte para saber mais sobre a aplicação de imagem corporativa.

[![Botão Iniciar sessão][AAD-Sign-In]][AAD-App-Branding]

Vamos observe a utilização do /common ponto final e a implementação de código em mais detalhe.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualizar o código para processar vários valores de emissor
As aplicações Web e web APIs recebem e validar os tokens do Azure AD.  

> [!NOTE]
> Apesar das aplicações de cliente nativo pedirem e recebem tokens do Azure AD, este procedimento é efetuado para enviá-los para APIs, onde são validados.  Aplicações nativas não validar os tokens e tem de tratá-los como opaco.
> 
> 

Vamos ver na forma como uma aplicação valida tokens recebe do Azure AD.  Uma aplicação de inquilino único demora, normalmente, um valor de ponto final, como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e utilizá-la para construir um URL de metadados (neste caso, OpenID Connect), como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Para transferir os dois tipos de informações críticos que são utilizados para validar os tokens: o inquilino da assinatura de chaves e o valor de emissor.  Cada inquilino do Azure AD tem um valor exclusivo do emissor com o formato:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

em que o valor GUID é a versão de mudança de nome seguro do ID de inquilino do inquilino.  Se clicar na ligação de metadados anterior para `contoso.onmicrosoft.com`, pode ver este valor de emissor no documento.

Quando uma aplicação de inquilino único valida um token, verifica a assinatura do token contra as chaves de assinatura do documento de metadados. Este teste permite para se certificar de que o valor de emissor de token de corresponde à que foi encontrado no documento de metadados.

Desde a /common ponto final não corresponde a um inquilino e não é um emissor, ao examinar o valor de emissor dos metadados de/comuns tem um URL transformada em modelo em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Por conseguinte, uma aplicação multi-inquilino não é possível validar os tokens apenas por correspondência do valor de emissor nos metadados com o `issuer` valor no token.  Uma aplicação multi-inquilino tem lógica para decidir quais os valores de emissor não são válidos e que tem não, com base no inquilino a parte de ID do valor de emissor.  

Por exemplo, se uma aplicação multi-inquilino só permite início de sessão de inquilinos específicos que tenham efetuado a inscrição do seu serviço, em seguida, este tem de verificar o valor de emissor ou `tid` valor no token para se certificar de que esse inquilino é na respetiva lista de subscritores de afirmação.  Se uma aplicação multi-inquilino apenas lida com indivíduos e não decisões qualquer acesso com base em inquilinos, em seguida, este pode ignorar o valor de emissor completamente.

Nos exemplos no multi-inquilinos de [relacionados conteúdo](#related-content) secção no final deste artigo, a validação do emissor está desativada para permitir que nenhum inquilino do Azure AD iniciar sessão.

Agora vamos ver a experiência de utilizador para os utilizadores que está a iniciar sessão para aplicações de multi-inquilinos.

## <a name="understanding-user-and-admin-consent"></a>Noções sobre utilizadores e de consentimento de admin
Para um utilizador iniciar sessão a uma aplicação no Azure AD, a aplicação tem de ser representada no inquilino do utilizador.  Isto permite que a organização fazer coisas como aplicar políticas únicas, quando os utilizadores a partir do respetivo inquilino iniciar sessão para a aplicação.  Para uma aplicação de inquilino único, este registo é simple; Este é o que acontece ao registar a aplicação no [portal do Azure][AZURE-portal].

Para uma aplicação multi-inquilino, o registo inicial para a aplicação se encontra no inquilino do Azure AD utilizado pelo programador.  Quando um utilizador de um inquilino diferente inicia sessão na aplicação pela primeira vez, o Azure AD pede-lhe-los para autorizar as permissões solicitadas pela aplicação.  Se estes consentimento, em seguida, uma representação da aplicação chamado um *principal de serviço* é criado no inquilino do utilizador, e início de sessão pode continuar. Uma delegação também é criada no diretório que regista o consentimento do utilizador para a aplicação. Consulte [objectos da aplicação e objetos de principais de serviço] [ AAD-App-SP-Objects] para obter detalhes sobre a aplicação de aplicações e ServicePrincipal objetos e como se relacionam entre si.

![Autorizar a aplicação de camada única][Consent-Single-Tier] 

Esta experiência de consentimento é afetada pelas permissões solicitadas pela aplicação.  AD do Azure suporta dois tipos de permissões, só a aplicação e delegados:

* Uma permissão de delegado atribui uma aplicação a capacidade para atuar como um utilizador com sessão iniciada para um subconjunto das ações que utilizador pode fazê-lo.  Por exemplo, pode conceder uma aplicação delegado permissão para ler o calendário do utilizador com sessão iniciada.
* Uma permissão só de aplicação é concedida diretamente para a identidade da aplicação.  Por exemplo, pode conceder uma aplicação a permissão só de aplicação para ler a lista de utilizadores de um inquilino, independentemente de quem tem sessão iniciada para a aplicação.

Alguns permissões podem ser autorizadas por um utilizador normal, enquanto outras precisam de consentimento de um administrador inquilino. 

### <a name="admin-consent"></a>Consentimento de Admin
Permissões só de aplicação necessitam sempre de consentimento de um administrador inquilino.  Se a aplicação solicite uma permissão só de aplicação e um utilizador tenta iniciar sessão na aplicação, é apresentada uma mensagem de erro a indicar que o utilizador não for capaz de consentimento.

Determinados delegadas permissões também necessitam de consentimento de um administrador inquilino.  Por exemplo, a capacidade de repetição de escrita para o Azure AD como utilizador com sessão iniciada, precisa de consentimento de um administrador inquilino.  Como permissões só de aplicação, se um utilizador comum tenta iniciar sessão para uma aplicação que solicita uma permissão de delegado que necessita de consentimento de administrador, a aplicação recebe um erro.  Se pretende ou não uma permissão requer consentimento de admin é determinado pelo programador que o recurso de publicados e pode ser encontrados na documentação para o recurso.  Ligações para tópicos que descrevem as permissões disponíveis para o Azure AD Graph API e o Microsoft Graph API são no [relacionados conteúdo](#related-content) secção deste artigo.

Se a sua aplicação utilizar permissões que necessitam de consentimento de administrador, tem de ter um gesto de como um botão ou a ligação em que o administrador pode iniciar a ação.  O pedido a aplicação envia para esta ação é um pedido de autorização OAuth2/OpenID Connect normal, mas que também inclui o `prompt=admin_consent` parâmetro de cadeia de consulta.  Depois do administrador consentiu e o principal de serviço é criado no inquilino do cliente, os pedidos subsequentes de início de sessão não é necessário o `prompt=admin_consent` parâmetro. Uma vez que o administrador tem de decidir que as permissões de pedido são aceitáveis, não existem outros utilizadores no inquilino recebem consentimento a partir desse ponto.

O `prompt=admin_consent` parâmetro também pode ser utilizado por aplicações que efetuam pedidos de permissões que não necessitam de consentimento de admin. Isto é feito quando o applicator requer uma experiência onde o inquilino "inscreve" uma hora e não a outros utilizadores administradores recebem consentimento a partir desse ponto no.

Se uma aplicação requer consentimento admin e um administrador inicia sessão, mas a `prompt=admin_consent` parâmetro não é enviado, o administrador permitir com êxito para a aplicação **apenas para a conta de utilizador**.  Os utilizadores regulares são ainda não conseguir iniciar sessão e autorizar a aplicação.  Esta funcionalidade é útil se pretender conceder ao administrador de inquilinos a capacidade para explorar a aplicação antes de permitir acesso a outros utilizadores.

Um administrador inquilino pode desativar a capacidade dos utilizadores regulares autorizar aplicações.  Se esta capacidade estiver desativada, o consentimento de admin é sempre necessário para a aplicação definir no inquilino.  Se pretender testar a aplicação com o consentimento do utilizador regular desativado, pode encontrar o parâmetro de configuração no inquilino do Azure AD secção de configuração a [portal do Azure][AZURE-portal].

> [!NOTE]
> Algumas aplicações pretendem uma experiência de onde os utilizadores regulares conseguem inicialmente o consentimento, e mais tarde a aplicação pode envolver as permissões de administrador e pedidos que necessitam de consentimento de admin.  Não há nenhuma forma de fazê-lo com um registo de aplicação único no Azure AD hoje.  O ponto de final de modelo de implementação de Gestor de recursos do Azure AD futuros permite que aplicações para pedir as permissões no tempo de execução, em vez de no momento de registo, que permite que este cenário.  Para obter mais informações, consulte o [modelo de implementação do Gestor de recursos do modelo de aplicação do Azure AD guia para programadores][AAD-V2-Dev-Guide].
> 
> 

### <a name="consent-and-multi-tier-applications"></a>Aplicações de consentimento e várias camadas
A aplicação pode ter várias camadas, cada um representado pelo seu próprio registo no Azure AD.  Por exemplo, uma aplicação nativa que chama uma API web ou uma aplicação web que chama uma API web.  Em ambos os nestes casos, o cliente (aplicação nativa ou aplicação web) pedidos de permissões para chamar o recurso (web API).  Para o cliente ser autorizado com êxito para o inquilino de um cliente, todos os recursos aos quais que solicita permissões já devem existir no inquilino do cliente.  Se esta condição não é cumprida, do Azure AD devolve um erro que o recurso tem de ser adicionado primeiro.

**Várias camadas de um único inquilino**

Isto pode ser um problema se a sua aplicação lógica é composta por dois ou mais registos de acesso aplicação, por exemplo, um cliente separado e recursos.  Como pode proceder para que o recurso para o inquilino de cliente primeiro?  Azure AD abrange neste caso, ativando o cliente e recursos para ser autorizado num único passo. O utilizador verá o total da soma de permissões solicitado pelo cliente e de recursos na página de consentimento.  Para ativar este comportamento, registo de aplicação do recurso tem de incluir o ID da aplicação do cliente como um `knownClientApplications` no respetivo manifesto de aplicação.  Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Esta propriedade pode ser atualizada através do recurso [manifesto da aplicação][AAD-App-Manifest]. Isto é demonstrado num multicamadas de cliente nativo ao exemplo de web API no [relacionados conteúdo](#related-content) secção no final deste artigo. O diagrama seguinte fornece uma descrição geral de consentimento para uma aplicação multicamada registada de um único inquilino:

![Autorizar a aplicação de cliente conhecidos de várias camadas][Consent-Multi-Tier-Known-Client] 

**Várias camadas em vários inquilinos**

Um cenário semelhante acontece se a diferentes camadas de uma aplicação estão registadas em diferentes inquilinos.  Por exemplo, considere o caso da criação de uma aplicação cliente nativa que chama a API do Office 365 Exchange Online.  Para desenvolver nativo aplicações e versões posterior para a aplicação nativa para execução no inquilino de um cliente, o Exchange Online principal de serviço tem de estar presente.  Neste caso, o programador e o cliente tem de comprar Exchange Online para o serviço principal a serem criadas os seus inquilinos.  

No caso de uma API criada por uma organização que não sejam Microsoft, o programador da API tem de fornecer uma forma para os seus clientes autorizar a aplicação para inquilinos dos seus clientes. Se a estrutura recomendada para o Programador de terceiros 3rd construir a API de forma a que o se também pode funcionar como um cliente web para implementar a inscrição:

1. Siga as secções anteriores para garantir que a API implementa os requisitos de registo/código de aplicação multi-inquilino
2. Para além de exposição âmbitos/funções a API, certifique-se inclui o registo de "iniciar sessão e perfil de utilizador de leitura" permissão do Azure AD (fornecido por predefinição)
3. Implementar uma página sessão-na/sessão-up no cliente web, seguindo o [admin consentimento](#admin-consent) orientações debatidas anteriormente 
4. Depois do utilizador permitir à aplicação, os serviço principal e do consentimento delegação as ligações são criadas no seu inquilino e a aplicação nativa pode obter tokens para a API

O diagrama seguinte fornece uma descrição geral de consentimento para uma aplicação multicamada registada em diferentes inquilinos:

![Autorizar a aplicação de múltiplos intervenientes de várias camada][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Revogar o consentimento
Os administradores e utilizadores podem revogar a autorização da aplicação em qualquer altura:

* Utilizadores revogarem o acesso a aplicações individuais, removendo-os partir do respetivo [aplicações do painel de acesso] [ AAD-Access-Panel] lista.
* Os administradores de revogar o acesso a aplicações, removendo-os partir do Azure AD utilizando a secção de gestão do Azure AD do [portal do Azure][AZURE-portal].

Se um administrador permitir a uma aplicação para todos os utilizadores de um inquilino, os utilizadores não é possível revogar o acesso individualmente.  Só o administrador pode revogar o acesso e apenas para toda a aplicação.

### <a name="consent-and-protocol-support"></a>Consentimento e suporte de protocolos
Consentimento é suportado no Azure AD através do OAuth, OpenID Connect, WS-Federation e protocolos SAML.  Os protocolos SAML e WS-Federation não suportam o `prompt=admin_consent` parâmetro, por isso consentimento admin só é possível através de OAuth e o OpenID Connect.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicações de multi-inquilinos e a colocação em cache de Tokens de acesso
Aplicações de multi-inquilinos também podem obter tokens de acesso para chamar as APIs que estão protegidas pelo Azure AD.  Um erro comum ao utilizar o Active Directory Authentication Library (ADAL) com uma aplicação multi-inquilino, para pedir um token de utilizador utilizando o /common, recebeu uma resposta de inicialmente, em seguida, pedir um token subsequente para esse mesmo utilizador também utilizando /common.  Uma vez que a resposta do Azure AD é proveniente de um inquilino, não/comuns, ADAL coloca em cache o token como sendo de inquilino. Chamada subsequente /common a obter um token de acesso para o utilizador a entrada de cache de pedidos sem êxito e é pedido ao utilizador para iniciar sessão novamente.  Para evitar em falta a cache, certificar-se de que são efetuadas chamadas subsequentes para um utilizador já com sessão iniciada para o ponto final do inquilino.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a criar uma aplicação que pode iniciar sessão um utilizador de qualquer inquilino do Azure Active Directory. Depois de ativar o início de sessão único entre a sua aplicação e o Azure Active Directory, também pode atualizar a aplicação de acesso a APIs expostas pelos recursos da Microsoft, como o Office 365. Por isso, pode oferecer uma experiência personalizada na sua aplicação, por exemplo que mostra a nível contextual das informações aos utilizadores, como a respetiva imagem do perfil ou os respetivos próxima sessão de calendário. Para mais informações sobre como fazer chamadas de API do Azure Active Directory e o Office 365 serviços como o Exchange, SharePoint, OneDrive, OneNote, Planner, Excel e muito mais, visite: [Microsoft Graph API][MSFT-Graph-overview].


## <a name="related-content"></a>Conteúdo relacionado
* [Exemplos de aplicação multi-inquilino][AAD-Samples-MT]
* [Diretrizes de imagem corporativa para aplicações][AAD-App-Branding]
* [Guia de AD para programadores do Azure][AAD-Dev-Guide]
* [Objetos da aplicação e objetos de principais de serviço][AAD-App-SP-Objects]
* [Integrar aplicações com o Azure Active Directory][AAD-Integrating-Apps]
* [Descrição geral do Framework consentimento][AAD-Consent-Overview]
* [Âmbitos de permissões de Microsoft Graph API][MSFT-Graph-permision-scopes]
* [Âmbitos de permissões do Azure AD Graph API][AAD-Graph-Perm-Scopes]

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














