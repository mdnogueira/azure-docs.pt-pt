---
title: "As políticas de API Management do Azure | Microsoft Docs"
description: "Saiba como criar, editar e configurar políticas na API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 537e5caf-708b-430e-a83f-72b70af28aa9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: ef4cb447430a613dd519d96dd7732a9349ebba39
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="policies-in-azure-api-management"></a>Políticas de API Management do Azure
Na API Management do Azure, as políticas são uma funcionalidade poderosa do sistema que permite ao publicador alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou resposta de uma API. As instruções populares incluem a conversão do formato de XML para JSON e limitação para restringir a quantidade de chamadas recebidas de um programador de taxa de chamadas. Muitas mais políticas estão disponíveis a box.

Consulte o [referência de política] [ Policy Reference] para uma lista completa das declarações de política e as respetivas definições.

As políticas são aplicadas no interior do gateway que se encontra entre os consumidores de API e a API gerida. O gateway recebe todos os pedidos e normalmente reencaminha-os será alterado para a API subjacente. No entanto uma política pode aplicar as alterações para o pedido de entrada e de resposta de saída.

As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como o [controlar o fluxo] [ Control flow] e [definir variável] [ Set variable] políticas baseadas em expressões de política. Para obter mais informações, consulte [políticas avançadas] [ Advanced policies] e [expressões de política][Policy expressions].

## <a name="scopes"></a>Como configurar políticas
As políticas podem ser configuradas globalmente ou no âmbito de um [produto][Product], [API] [ API] ou [operação][Operation]. Para configurar uma política, navegue para o editor de políticas no portal do publicador.

![Menu de políticas][policies-menu]

O editor de políticas é composto por três secções principais: o âmbito da política (superior), a definição de política em que as políticas forem editadas (esquerda) e as declarações de listam (direita):

![Editor de políticas][policies-editor]

Para começar a configurar uma política tem de selecionar primeiro o âmbito no qual a política deve ser aplicada. Na captura de ecrã abaixo o **Starter** produto estiver selecionado. Tenha em atenção que o símbolo quadrado junto ao nome da política indica que uma política já está aplicada neste nível.

![Âmbito][policies-scope]

Uma vez que já foi aplicada uma política, a configuração é apresentada na vista de definição.

![Configurar][policies-configure]

A política é apresentada como só de leitura em primeiro lugar. Para editar a definição, clique em de **configurar a política de** ação.

![Editar][policies-edit]

A definição de política é um simple um documento XML que descreve uma sequência de declarações de entrada e saídas. O XML pode ser editado diretamente na janela de definição. É fornecida uma lista de instruções para a direita e instruções aplicáveis ao âmbito atual estiverem ativadas e realçadas; como é demonstrado pelo **limitar taxa de chamadas** instrução na captura de ecrã acima.

Clicar numa instrução ativada irá adicionar o XML adequado na localização de cursor na vista de definição. 

> [!NOTE]
> Se a política que pretende adicionar não estiver ativada, certifique-se de que está no âmbito correto para essa política. Cada declaração de política foi concebida para utilização num determinado âmbitos e as secções de política. Para rever as secções de política e os âmbitos de uma política, verifique o **utilização** secção para essa política no [referência de política][Policy Reference].
> 
> 

Uma lista completa das declarações de política e as definições estão disponíveis no [referência de política][Policy Reference].

Por exemplo, para adicionar uma nova declaração para restringir os pedidos recebidos para endereços IP especificados, coloque o cursor apenas dentro do conteúdo do `inbound` elemento XML e clique em de **restringir chamador IPs** instrução.

![Políticas de restrição][policies-restrict]

Isto irá adicionar um fragmento XML para o `inbound` elemento que fornece orientações sobre como configurar a instrução.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Para limitar os pedidos de entrada e aceitar apenas os partir de um endereço IP de 1.2.3.4 modificam o XML da seguinte forma:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

![Guardar][policies-save]

Quando terminar de configurar as instruções para a política, clique em **guardar** e as alterações serão propagadas para o gateway de gestão de API imediatamente.

## <a name="sections"></a>Configuração da política de compreender
Uma política é uma série de declarações executado por ordem para um pedido e resposta. A configuração está dividida adequadamente `inbound`, `backend`, `outbound`, e `on-error` secções conforme mostrado na configuração seguinte.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Se ocorrer um erro durante o processamento de um pedido, os restantes passos no `inbound`, `backend`, ou `outbound` secções são ignoradas e execução salta para as instruções no `on-error` secção. Colocando declarações de política no `on-error` secção pode rever o erro, utilizando o `context.LastError` propriedade, Inspecione e personalizar a resposta de erro utilizando o `set-body` política e configurar o que acontece se ocorrer um erro. Existem códigos de erro para obter os passos incorporados e de erros que possam ocorrer durante o processamento de declarações de política. Para obter mais informações, consulte [erros nas políticas de gestão de API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Uma vez que as políticas podem ser especificadas em diferentes níveis (global, produto, api e operação) a configuração fornece uma forma para que possa especificar a ordem em que as instruções a definição de política executar no que respeita à política de principal. 

Âmbitos de política são avaliados pela seguinte ordem.

1. Âmbito global
2. Âmbito do produto
3. Âmbito de API
4. Âmbito de operação

As instruções dentro delas são avaliadas de acordo com a colocação do `base` elemento, se estiver presente. Política global não tem um principal de política e utilizar o `<base>` elemento no mesmo não tem qualquer efeito.

Por exemplo, se tiver uma política ao nível global e uma política configurada para uma API, em seguida, sempre que essa API específica é utilizada ambas as políticas serão aplicadas. Gestão de API permite a ordenação determinista de declarações de política combinado através do elemento base. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Na definição de política de exemplo acima, o `cross-domain` instrução iria executar antes de ser seguido de qualquer políticas superiores que seriam por sua vez, o `find-and-replace` política. 

Para ver as políticas no âmbito atual no editor de política, clique em **recalcular política eficaz para âmbito selecionado**.

## <a name="next-steps"></a>Passos seguintes
Veja a seguir as vídeo em expressões de política.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
