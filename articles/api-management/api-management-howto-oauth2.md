---
title: Autorizar a contas de programador com OAuth 2.0 na API Management do Azure | Microsoft Docs
description: Saiba como autorizar os utilizadores com OAuth 2.0 na API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 78c48247-64f0-4708-b2d0-98b61a821283
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: c61b4969757d62d7e16e7acecf7d7bed4b31c9b1
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar contas de programador com OAuth 2.0 na API Management do Azure
Suportam muitas APIs [OAuth 2.0](http://oauth.net/2/) para proteger a API e certifique-se de que apenas os utilizadores válidos têm acesso, e apenas podem aceder a recursos a que está a elegível. Para utilizar a consola de programador interativo da API Management do Azure com essas APIs, o serviço permite-lhe configurar a sua instância de serviço funcione com o OAuth 2.0 API ativada.

## <a name="prerequisites"></a>Pré-requisitos
Este guia mostra-lhe como configurar a sua instância do serviço de gestão de API a utilizar a autorização do OAuth 2.0 para contas de programador, mas não mostrar-lhe como configurar um fornecedor de OAuth 2.0. A configuração para cada fornecedor de OAuth 2.0 é diferente, embora os passos são semelhantes e as necessárias peças de informações utilizadas na configuração de OAuth 2.0 na sua instância do serviço de API Management são os mesmos. Este tópico mostra exemplos que utilizam o Azure Active Directory como um fornecedor de OAuth 2.0.

> [!NOTE]
> Para obter mais informações sobre a configuração de OAuth 2.0, utilizando o Azure Active Directory, consulte o [WebApp-GraphAPI-DotNet] [ WebApp-GraphAPI-DotNet] exemplo.
> 
> 

## <a name="step1"></a>Configurar um servidor de autorização do OAuth 2.0 na API Management
Para começar, clique em **Portal do editor** no Portal do Azure para o seu serviço de Gestão de API.

![Portal do publicador][api-management-management-console]

> [!NOTE]
> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **segurança** do **API Management** menu à esquerda, clique em **OAuth 2.0**e, em seguida, clique em **Adicionar servidor de autorização**.

![OAuth 2.0][api-management-oauth2]

Depois de clicar em **Adicionar servidor de autorização**, é apresentado o novo formato de servidor de autorização.

![Novo servidor][api-management-oauth2-server-1]

Introduza um nome e uma descrição opcional no **nome** e **Descrição** campos. 

> [!NOTE]
> Estes campos são utilizados para identificar o servidor de autorização do OAuth 2.0 na instância de serviço de API de gestão atual e os respetivos valores não teve origem no servidor de OAuth 2.0.
> 
> 

Introduza o **URL de página de registo de cliente**. Esta página é onde os utilizadores podem criar e gerir as respetivas contas e varia consoante o fornecedor de OAuth 2.0 utilizado. O **URL de página de registo de cliente** aponta para a página que os utilizadores podem utilizar para criar e configurar as suas próprias contas para fornecedores de OAuth 2.0 que suportem gestão de contas de utilizadores. Algumas organizações não configurar nem utilizar esta funcionalidade, mesmo se o fornecedor de OAuth 2.0 suporta. Se o fornecedor de OAuth 2.0 não tiver a gestão de utilizadores de contas configuradas, introduza um URL de marcador de posição aqui, tais como o URL da sua empresa ou um URL como `https://placeholder.contoso.com`.

A secção seguinte do formulário contém o **tipos de concessão de códigos de autorização**, **URL de ponto final de autorização**, e **método de pedido de autorização** definições.

![Novo servidor][api-management-oauth2-server-2]

Especifique o **tipos de concessão de códigos de autorização** verificando os tipos de pretendido. **Código de autorização** é especificado por predefinição.

Introduza o **URL de ponto final de autorização**. Para o Azure Active Directory, este URL será semelhante ao URL seguinte, onde `<client_id>` é substituído com o id de cliente que identifica a sua aplicação para o servidor de OAuth 2.0.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

O **método de pedido de autorização** Especifica como o pedido de autorização é enviado para o servidor de OAuth 2.0. Por predefinição **obter** está selecionada.

A secção seguinte é onde o **URL de ponto final do Token**, **métodos de autenticação de cliente**, **enviar o método de token de acesso**, e **predefinido âmbito** especificados.

![Novo servidor][api-management-oauth2-server-3]

Para um servidor do Azure Active Directory OAuth 2.0, o **URL de ponto final do Token** terá o seguinte formato, onde `<APPID>` tem o formato de `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

A definição predefinida para **métodos de autenticação de cliente** é **básico**, e **enviar o método de token de acesso** é **cabeçalho de autorização**. Estes valores são configurados nesta secção do formulário, juntamente com o **predefinido âmbito**.

O **credenciais do cliente** secção contém o **ID de cliente** e **segredo do cliente**, que são obtidos durante o processo de criação e configuração do seu servidor de OAuth 2.0. Uma vez a **ID de cliente** e **segredo do cliente** forem especificados, o **redirect_uri** para o **código de autorização** é gerado. Este URI é utilizado para configurar o URL de resposta na sua configuração de servidor de OAuth 2.0.

![Novo servidor][api-management-oauth2-server-4]

Se **tipos de concessão de códigos de autorização** está definido como **palavra-passe de proprietário de recursos**, a **credenciais de palavra-passe de proprietário do recurso** secção é utilizada para especificar as credenciais; caso contrário, pode deixar em branco.

![Novo servidor][api-management-oauth2-server-5]

Assim que o formulário estiver concluído, clique em **guardar** para guardar a configuração de servidor de autorização de API Management OAuth 2.0. Depois da configuração do servidor é guardada, pode configurar as APIs utilizem esta configuração, conforme mostrado na secção seguinte.

## <a name="step2"></a>Configurar uma API para utilizar a autorização de utilizador de OAuth 2.0
Clique em **APIs** do **API Management** menu da esquerda, clique o nome da API pretendido, clique em **segurança**e, em seguida, selecione a caixa para **OAuth 2.0**.

![Autorização do utilizador][api-management-user-authorization]

Selecione o pretendido **servidor autorização** na lista pendente e clique em **guardar**.

![Autorização do utilizador][api-management-user-authorization-save]

## <a name="step3"></a>Testar a autorização de utilizador de OAuth 2.0 no Portal do Programador
Depois de ter configurado o servidor de autorização do OAuth 2.0 e configurado a API a utilizar esse servidor, pode testá-lo ao aceder ao Portal do programador e chamar uma API.  Clique em **Portal do programador** no menu superior à direita.

![Portal do programador][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API eco**.

![API Eco][api-management-apis-echo-api]

> [!NOTE]
> Se tiver apenas uma API configurada ou visível para a sua conta, clicar em APIs leva-o diretamente para as operações dessa API.
> 
> 

Selecione o **recurso GET** operação, clique em **abrir a consola**e, em seguida, selecione **código de autorização** da lista pendente.

![Abrir consola][api-management-open-console]

Quando **código de autorização** está selecionada, é apresentada uma janela de pop-up com o formulário de início de sessão do fornecedor de OAuth 2.0. Neste exemplo, o formulário de início de sessão é fornecido pelo Azure Active Directory.

> [!NOTE]
> Se tiver desativados de pop-ups lhe-á para ativá-los pelo browser. Depois de ativá-los, selecione **código de autorização** novamente e será apresentado o formulário de início de sessão.
> 
> 

![Iniciar sessão][api-management-oauth2-signin]

Depois de ter iniciado sessão, o **cabeçalhos de pedido** são povoadas com um `Authorization : Bearer` cabeçalho que autoriza o pedido.

![Token de cabeçalho de pedido][api-management-request-header-token]

Nesta altura pode configurar os valores pretendidos para os restantes parâmetros e submeter o pedido. 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como utilizar o OAuth 2.0 e a API Management, consulte o seguinte vídeo e associado [artigo](api-management-howto-protect-backend-with-aad.md).

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

