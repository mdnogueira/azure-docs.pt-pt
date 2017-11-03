---
title: "As restrições e os problemas conhecidos na importação de API de gestão do Azure API | Microsoft Docs"
description: "Detalhes de problemas conhecidos e restrições a importar para utilizar os formatos de API aberta, WSDL ou WADL de API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
ms.openlocfilehash: 4cb6ad53b59b81f906a85027f4ff988bbb78706a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de API e problemas conhecidos
## <a name="about-this-list"></a>Sobre esta lista
Enquanto cada esforço é feito para garantir que importar a API para a API Management do Azure é como totalmente integrada e libertar problema quanto possível, iremos ocasionalmente impõe restrições ou identificar problemas que necessitam de ser retificado antes de importar com êxito. Este artigo documentos estes, organised pelo formato importação da API.

## <a name="open-api"></a>Abrir API/Swagger
Em geral, se está a receber erros de importar o documento de API aberta,. Certifique-se de que tem de validá-la - o utilizando o estruturador no Portal do Azure novo (estrutura - Front-End - abra API especificação Editor) ou com um 3rd terceiros, tais como ferramenta <a href="http://www.swagger.io">Swagger Editor de</a>.

* **Nome de anfitrião** é necessário um atributo de nome de anfitrião.
* **Caminho de base** é necessário um atributo de caminho de base.
* **Os esquemas** Requeremos uma matriz de esquema. 

## <a name="wsdl"></a>WSDL
Ficheiros WSDL são utilizados para gerar SOAP Pass-through APIs ou servem como o back-end de uma API SOAP-REST.

* **WSDL: import** estamos atualmente não suportam as APIs utilizando este atributo. Os clientes devem intercalar os elementos importados para um documento.
* **As mensagens com várias partes** não são atualmente suportadas.
* **WCF wsHttpBinding** serviços SOAP criados com o Windows Communication Foundation devem utilizar o basicHttpBinding, wsHttpBinding não é suportada.
* **MTOM** serviços que utilizam MTOM <em>poderá</em> funcione. Oficial de suporte não é oferecido neste momento.
* **Recursão** tipos que estão definidos em modo recursivo (por exemplo, consulte a uma matriz dos próprios) não são suportadas.

## <a name="wadl"></a>WADL
Existem atualmente não existem problemas de importação WADL conhecidos.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
