---
title: Como criar APIs na API Management do Azure
description: Saiba como criar e configurar APIs na API Management do Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 14c20da4-f29f-4b28-bec7-3d4c50b734da
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 79630fb8998d47aab8eed38daf7d5fcef3d870d7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-apis-in-azure-api-management"></a>Como criar APIs na API Management do Azure
Uma API na API Management representa um conjunto de operações que podem ser invocados por aplicações cliente. Novas APIs são criadas no portal do publicador e, em seguida, são adicionadas as operações pretendidas. Depois das operações são adicionadas, a API é adicionada um produto e pode ser publicada. Depois de uma API for publicada, pode ser subscrito e utilizado pelos programadores.

Este guia mostra o primeiro passo no processo de: como criar e configurar uma nova API na API Management. Para obter mais informações sobre a adição de operações e publicar um produto, consulte [como adicionar operações a uma API] [ How to add operations to an API] e [como criar e publicar um produto] [ How to create and publish a product].

## <a name="create-new-api"></a>Criar uma nova API
As APIs são criadas e configuradas no portal do publicador. Para aceder ao portal do publicador, clique em **portal do publicador** no Portal do Azure para o seu serviço de API Management.

![Portal do publicador][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **APIs** do **API Management** menu à esquerda e, em seguida, clique em **adicionar API**.

![Criar API][api-management-create-api]

Utilize o **Adicionar nova API** janela para configurar a nova API.

![Adicionar nova API][api-management-add-new-api]

Os campos seguintes são utilizados para configurar a nova API.

* **Nome da Web API** fornece um nome único e descritivo para a API. Será apresentado no portais do programador e o publicador.
* **URL do serviço Web** referencia o serviço HTTP implementar a API. API de gestão reencaminha os pedidos para este endereço.
* **Sufixo do URL da API Web** é anexado ao URL de base para o serviço de gestão de API. A base de URL é comum para todos os APIs alojadas por uma instância de serviço de API Management. Gestão de API distingue APIs pelo respetivo sufixo e, por conseguinte, o sufixo tem de ser exclusivo para cada API para um determinado publicador.
* **Esquema de URL de API Web** determina quais protocolos podem ser utilizados para aceder à API do. HTTPs é especificado por predefinição.
* Opcionalmente, adicione esta nova API um produto, clique em de **produtos (opcionais)** pendente e escolha um produto. Este passo pode ser repetido múltiplas vezes para adicionar a API a vários produtos.

Assim que os valores pretendidos estiverem configurados, clique em **guardar**. Uma vez criada a nova API, é apresentada a página de resumo para a API no portal do publicador.

![Resumo da API][api-management-api-summary]

## <a name="configure-api-settings"></a>As definições da API de configurar
Pode utilizar o **definições** separador para verificar e edite a configuração de uma API. **Nome da Web API**, **URL do serviço Web**, e **sufixo do URL da API Web** são inicialmente definido quando a API é criada e pode ser modificada aqui. **Descrição** fornece uma descrição opcional e **esquema de URL de API Web** determina quais protocolos podem ser utilizados para aceder à API do.

![Definições da API][api-management-api-settings]

Para configurar a autenticação de gateway para o serviço de back-end implementar a API, selecione o **segurança** separador. O **com credenciais** pendente pode ser utilizado para configurar **HTTP básico** ou **certificados de cliente** autenticação. Para utilizar autenticação básica de HTTP, basta introduza as credenciais pretendidas. Para obter informações sobre como utilizar a autenticação de certificado de cliente, consulte [como proteger serviços de back-end utilizando o cliente de autenticação de certificado na API Management do Azure][How to secure back-end services using client certificate authentication in Azure API Management].

O **segurança** separador também pode ser utilizado para configurar **autorização do utilizador** utilizando OAuth 2.0. Para obter mais informações, consulte [como autorizar contas de programador na API Management do Azure utilizando o OAuth 2.0][How to authorize developer accounts using OAuth 2.0 in Azure API Management].

![Definições de autenticação básica][api-management-api-settings-credentials]

Clique em **guardar** guardar quaisquer alterações efetuadas às definições de API.

## <a name="next-steps"> </a>Passos seguintes
Assim que é criada uma API e as definições configuradas, os passos seguintes são adicionar as operações para a API, adicione a API de um produto e publicá-lo para que fique disponível para programadores. Para obter mais informações, consulte os artigos seguintes.

* [Como adicionar operações a uma API][How to add operations to an API]
* [Como criar e publicar um produto][How to create and publish a product]

[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[How to secure back-end services using client certificate authentication in Azure API Management]: api-management-howto-mutual-certificates.md
[How to authorize developer accounts using OAuth 2.0 in Azure API Management]: api-management-howto-oauth2.md
