---
title: "Como adicionar operações a uma API na API Management do Azure | Microsoft Docs"
description: "Saiba como adicionar operações a uma API na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 1158a023-1913-4e9c-93de-9164b672f9b3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 8b047c0826590d1cb6a79a2f14ca07764dc2b409
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Como adicionar operações a uma API na API Management do Azure
Antes de pode ser utilizada uma API na API Management, operações tem de ser adicionadas. Este guia mostra como adicionar e configurar diferentes tipos de operações a uma API na API Management.

## <a name="add-operation"></a>Adicionar uma operação
As operações são adicionadas e configuradas a uma API no portal do publicador. Para aceder ao portal do publicador, clique em **portal do publicador** no Portal do Azure para o seu serviço de API Management.

![Portal do publicador][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

Selecione a API pretendida no portal do publicador e, em seguida, selecione o **operações** separador. 

![Operações][api-management-operations]

Clique em **adicionar operação** para adicionar uma operação de novo. O **nova operação** será apresentado e o **assinatura** separador será selecionado por predefinição.

![Adicionar a operação][api-management-add-operation]

Especifique o **verbo HTTP** escolhendo na lista pendente.

![Método HTTP][api-management-http-method]

<a name="url-template"></a>

Defina o modelo de URL ao escrever um fragmento de URL constituída por um ou mais segmentos de caminho de URL e zero ou mais parâmetros de cadeia de consulta. O modelo de URL, anexado ao URL de base da API, identifica uma única operação de HTTP. Pode conter um ou mais denominado partes variáveis que são identificadas pela chavetas. Estas partes variável são denominados os parâmetros do modelo e são atribuídas dinamicamente valores extraídos a partir do URL do pedido quando o pedido está a ser processado pela plataforma de gestão de API.

> O modelo de URL pode incluir padrões de carateres universais. Por exemplo, especificar `/*` irão reencaminhar todos os pedidos para este método HTTP para o back end serviço.

![Modelo de URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Se assim o desejar, especifique o **modelo Rewrite URL**. Isto permite-lhe utilizar o modelo de URL padrão para processamento de pedidos recebidos no front-end, ao chamar o back-end através de um URL, de acordo com o modelo de reescrever convertido. Os parâmetros do modelo do modelo de URL devem ser utilizados no modelo de reescrever. O exemplo seguinte mostra o tipo de conteúdo como codificado como segmento de caminho no serviço web do exemplo anterior, pode ser fornecido como um parâmetro de consulta na API publicado através de plataforma de gestão de API utilizando os modelos de URL.

![Reescrever de modelo de URL][api-management-url-template-rewrite]

Os chamadores à operação utilizarão o formato `/customers?customerid=ALFKI` e isto será mapeado para `/Customers('ALFKI')` quando o serviço de back-end é invocado.

**Apresentar** nome e **Descrição** forneça uma descrição da operação e são utilizados para fornecer a documentação para programadores com esta API no portal do programador.

![Descrição][api-management-description]

A descrição da operação pode ser especificada como texto simples ou HTML no **Descrição** caixa de texto.

## <a name="operation-caching"></a>Operação colocação em cache
Resposta a colocação em cache reduz a latência interpretada pela consumidores de API, reduz o consumo de largura de banda e diminuições a carga na HTTP web do serviço implementar a API. 

Para mais rápida e facilmente ativar colocação em cache para a operação, selecione o **colocação em cache** separador e verifique o **ativar** caixa de verificação.

![Colocação em cache][api-management-caching-tab]

**Duração** Especifica o período de tempo durante os quais a resposta da operação permanece na cache. O valor predefinido é de 3600 segundos ou 1 hora.

As chaves da cache são utilizadas para distinguir entre as respostas para que a resposta correspondente a cada chave de cache diferente irá obter o seu próprio valor em cache separado. Opcionalmente, introduza os parâmetros de cadeia de consulta específico e/ou os cabeçalhos de HTTP para ser utilizado em informática valores de chave de cache no **variação por parâmetros de cadeia de consulta** e **variação por cabeçalhos** caixas de texto, respetivamente. Quando nenhuma é o URL de pedido especificado, completo e os seguintes valores de cabeçalho HTTP são utilizados na geração de chaves de cache: **aceitar** e **conjunto de carateres aceitar**.

> Para obter mais informações sobre a colocação em cache e colocação em cache de políticas, consulte [como colocar em cache a operação resulta na API Management do Azure][How to cache operation results in Azure API Management].
> 
> 

## <a name="request-parameters"></a>Parâmetros do pedido
Parâmetros de operação são geridos no separador de parâmetros. Os parâmetros especificados no **URL modelo** no **assinatura** separador são adicionados automaticamente e podem ser alteradas apenas ao editar o modelo de URL. Os parâmetros adicionais podem ser introduzidos manualmente.

Para adicionar um novo parâmetro de consulta, clique em **Adicionar parâmetro de consulta** e introduza as seguintes informações:

* **Nome** -nome do parâmetro.
* **Descrição** -uma breve descrição do parâmetro (opcional).
* **Tipo** -tipo de parâmetro, selecionado na lista pendente.
* **Valores** -valores que podem ser atribuídos a este parâmetro. Um dos valores pode ser assinalado como predefinição (opcional).
* **Necessário** -tornar o parâmetro obrigatório marcando a caixa de verificação. 

![Parâmetros do pedido][api-management-request-parameters]

## <a name="request-body"></a>Corpo do pedido
Se a operação permite (por exemplo, PUT, POST) e necessita de um corpo pode fornecer um exemplo do mesmo em todas os formatos de representação suportadas (por exemplo, XML, json). 

> O corpo do pedido é utilizado para fins de documentação e não é validado.
> 
> 

Para introduzir um corpo do pedido, mude para o **corpo** separador.

Clique em **adicionar representação**, comece a escrever o nome de tipo de conteúdo pretendida (por exemplo, application/json), selecione-o na lista pendente e cole o exemplo de corpo do pedido pretendido no formato de selecionado a caixa de texto. 

![Corpo do pedido][api-management-request-body]

No adicionais para representações, também pode especificar uma descrição opcional texto no **Descrição** caixa de texto.

## <a name="responses"></a>Respostas
É uma boa prática para fornecer exemplos de respostas para todos os códigos de estado que pode produzir a operação. Cada código de estado pode ter mais do que um exemplo de corpo de resposta, uma para cada um dos tipos de conteúdo suportados. 

Para adicionar uma resposta, clique em **adicionar** e comece a escrever o código de estado pretendido. Neste exemplo é o código de estado **200 OK**. Depois do código é apresentado na lista pendente, selecione-a e o código de resposta é criado e adicionado para a operação.

![Código de resposta][api-management-response-code]

Clique em **adicionar representação**, comece a escrever o nome de tipo de conteúdo pretendida (por exemplo, application/json) e, em seguida, selecione-o na lista pendente.

![Tipo de corpo de conteúdo][api-management-response-body-content-type]

Cole o exemplo de corpo de resposta no formato de selecionado a caixa de texto. 

![Corpo da resposta][api-management-response-body]

Se assim o desejar, adicione uma descrição opcional para o **Descrição** caixa de texto.

Depois de ter configurada a operação, clique em **guardar**.

## <a name="next-steps"> </a>Passos seguintes
Assim que as operações são adicionadas a uma API, o passo seguinte é a API de associar a um produto e publicá-lo para que os programadores podem chamar as suas operações.

* [Como criar e publicar um produto][How to create and publish a product]

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
