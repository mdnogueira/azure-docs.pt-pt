---
title: "Como utilizar propriedades nas políticas de API Management do Azure"
description: "Saiba como utilizar propriedades nas políticas de API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 517f434c8f7fabc1402fb938d5ff5c733b86f2fd
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Como utilizar propriedades nas políticas de API Management do Azure
Políticas de API Management são uma funcionalidade poderosa do sistema que permite ao publicador alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. Declarações de política podem ser construídas com valores de texto literal, expressões de política e as propriedades. 

Cada instância de serviço de API Management tem uma coleção de propriedades de pares chave/valor que são globais para a instância de serviço. Estas propriedades podem ser utilizadas para gerir os valores de cadeia constante em todas as políticas de configuração da API e. Cada propriedade tem os seguintes atributos.

| Atributo | Tipo | Descrição |
| --- | --- | --- |
| Nome |Cadeia |O nome da propriedade. Pode conter apenas letras, dígitos, período, dash e carateres de caráter de sublinhado. |
| Valor |Cadeia |O valor da propriedade. Não pode estar vazios ou consistir apenas em espaço em branco. |
| Segredo |Valor booleano |Determina se o valor é um segredo e deve ser encriptado ou não. |
| Etiquetas |Matriz da cadeia |Opcional de etiquetas que quando é fornecido pode ser utilizado para filtrar a lista de propriedades. |

Propriedades são configuradas no portal do publicador no **propriedades** separador. No exemplo seguinte, as três propriedades estão configuradas.

![Propriedades][api-management-properties]

Os valores de propriedade podem conter cadeias literais e [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx). A tabela seguinte mostra as propriedades de três exemplo anterior e respetivos atributos. O valor de `ExpressionProperty` é uma expressão de política que devolve uma cadeia contendo a data e hora atuais. A propriedade `ContosoHeaderValue` está marcada como um segredo, pelo que não é apresentado o respetivo valor.

| Nome | Valor | Segredo | Etiquetas |
| --- | --- | --- | --- |
| ContosoHeader |trackingId |Falso |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |Verdadeiro |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |Falso | |

## <a name="to-use-a-property"></a>Para utilizar uma propriedade
Para utilizar uma propriedade de uma política, coloque o nome da propriedade dentro de um par duplo de chavetas como `{{ContosoHeader}}`, conforme mostrado no exemplo seguinte.

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é utilizado como o nome de um cabeçalho num `set-header` política, e `ContosoHeaderValue` é utilizado como o valor de que o cabeçalho. Quando esta política é avaliada durante um pedido ou resposta para o gateway de gestão de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídos com os respetivos valores de propriedade correspondentes.

Propriedades podem ser utilizadas como atributo concluído ou valores de elemento, conforme mostrado no exemplo anterior, mas também podem ser inseridas ou combinados com parte de uma expressão de texto literal, conforme mostrado no exemplo seguinte:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Propriedades também podem conter expressões de política. No exemplo seguinte, o `ExpressionProperty` é utilizado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando esta política é avaliada, `{{ExpressionProperty}}` é substituído com o respetivo valor: `@(DateTime.Now.ToString())`. Uma vez que o valor de uma expressão de política, a expressão é avaliada e a política continua com a execução.

Pode testar horizontalmente no portal do programador ao chamar uma operação que tem uma política com as propriedades do âmbito. No exemplo seguinte, uma operação é chamada com o exemplo anterior dois `set-header` políticas com propriedades. Tenha em atenção que a resposta contém dois cabeçalhos personalizados que foram configurados através de políticas com propriedades.

![Portal do programador][api-management-send-results]

Se observar o [rastreio API Inspector](api-management-howto-api-inspector.md) para uma chamada de que inclui as duas políticas de exemplo anterior com propriedades, pode ver as duas `set-header` políticas com os valores de propriedade inseridas, bem como a avaliação da expressão de política para a propriedade que continha a expressão de política.

![Rastreio de API Inspector][api-management-api-inspector-trace]

Tenha em atenção que, enquanto os valores de propriedade podem conter expressões de política, os valores de propriedade não podem conter outras propriedades. Se o texto que contém uma referência de propriedade é utilizado para um valor de propriedade, tais como `Property value text {{MyProperty}}`, esse referência da propriedade não será substituída e serão incluída como parte do valor de propriedade.

## <a name="to-create-a-property"></a>Para criar uma propriedade
Para criar uma propriedade, clique em **Adicionar propriedade** no **propriedades** separador.

![Adicionar propriedade][api-management-properties-add-property-menu]

**Nome** e **valor** são os valores necessários. Se este valor de propriedade é um segredo, verifique o **este é um segredo** caixa de verificação. Introduza uma ou mais etiquetas opcionais para ajudar a organizar as propriedades e clique em **guardar**.

![Adicionar propriedade][api-management-properties-add-property]

Quando uma nova propriedade for guardada, a **Procurar propriedade** caixa de texto é preenchida com o nome da nova propriedade e é apresentada a nova propriedade. Para apresentar todas as propriedades, desmarque a **Procurar propriedade** caixa de texto e prima introduza.

![Propriedades][api-management-properties-property-saved]

Para obter informações sobre a criação de uma propriedade utilizando a API REST, consulte [criar uma propriedade utilizando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Para editar uma propriedade
Para editar uma propriedade, clique em **editar** junto a propriedade para editar.

![Editar a propriedade][api-management-properties-edit]

Efetue as alterações pretendidas e clique em **guardar**. Se alterar o nome da propriedade, as políticas que referenciem essa propriedade são atualizadas automaticamente para utilizar o novo nome.

![Editar a propriedade][api-management-properties-edit-property]

Para informações sobre como editar uma propriedade utilizando a API REST, consulte [editar uma propriedade utilizando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Para eliminar uma propriedade
Para eliminar uma propriedade, clique em **eliminar** junto a propriedade para eliminar.

![Eliminar a propriedade][api-management-properties-delete]

Clique em **Sim, elimine-o** para confirmar.

![Confirmar eliminação][api-management-delete-confirm]

> [!IMPORTANT]
> Se a propriedade é referenciada pelas políticas, será possível eliminá-lo com êxito até que remova a propriedade de todas as políticas que a utilizam.
> 
> 

Para obter informações sobre como eliminar uma propriedade utilizando a API REST, consulte [eliminar uma propriedade utilizando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Para procurar e filtrar as propriedades
O **propriedades** separador inclui a procura e filtragem de funcionalidades para o ajudar a gerir as propriedades. Para filtrar a lista de propriedades por nome de propriedade, introduza um termo de pesquisa no **Procurar propriedade** caixa de texto. Para apresentar todas as propriedades, desmarque a **Procurar propriedade** caixa de texto e prima introduza.

![Pesquisa][api-management-properties-search]

Para filtrar a lista de propriedades por valores de etiqueta, introduza uma ou mais etiquetas para o **filtrar por etiquetas** caixa de texto. Para apresentar todas as propriedades, desmarque a **filtrar por etiquetas** caixa de texto e prima introduza.

![Filtro][api-management-properties-filter]

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como trabalhar com políticas
  * [Políticas de gestão de API](api-management-howto-policies.md)
  * [Referência de políticas](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Veja uma descrição geral do vídeo
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

