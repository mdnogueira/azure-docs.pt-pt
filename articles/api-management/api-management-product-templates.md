---
title: Modelos de produtos na API Management do Azure | Microsoft Docs
description: "Saiba como personalizar o conteúdo das páginas de produto no portal do Programador de API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: dae757231d8f2ff7fcd8e032d941c0fa9f192796
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="product-templates-in-azure-api-management"></a>Modelos de produtos na API Management do Azure
Gestão de API do Azure fornece a capacidade para personalizar os conteúdos de páginas de portal de programador utilizando um conjunto de modelos que configurar o respetivo conteúdo. Utilizando [DotLiquid](http://dotliquidmarkup.org/) sintaxe e o editor à sua escolha, tal como [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e um conjunto de fornecido localizado [recursos de cadeia](api-management-template-resources.md#strings), [recursos de glifo](api-management-template-resources.md#glyphs), e [controlos de página](api-management-page-controls.md), tem uma enorme flexibilidade para configurar o conteúdo das páginas como julgar utilizando estes modelos.  
  
 Os modelos nesta secção permitem-lhe personalizar os conteúdos das páginas de produto no portal do programador.  
  
-   [Lista de produto](#ProductList)  
  
-   [Produto](#Product)  
  
> [!NOTE]
>  Modelos predefinidos de exemplo estão incluídos na documentação do seguinte, mas estão sujeitos a alterações devido a melhoramentos contínuos. Pode ver os modelos predefinidos em direto no portal do programador, navegando para os modelos de individuais pretendidos. Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="ProductList"></a>Lista de produto  
 O **lista produto** modelo permite-lhe personalizar o corpo da página de lista de produtos no portal do programador.  
  
 ![Lista de produtos](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Modelo predefinido  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Controlos  
 O `Product list` modelo pode utilizar o seguinte [página controlos](api-management-page-controls.md).  
  
-   [controlo de paginação](api-management-page-controls.md#paging-control)  
  
-   [controlo de procura](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Paginação|[Paginação](api-management-template-data-model-reference.md#Paging) entidade.|As informações de paginação para a coleção de produtos.|  
|Filtragem|[Filtragem](api-management-template-data-model-reference.md#Filtering) entidade.|As informações de filtragem para a página de lista de produtos.|  
|Produtos|Coleção de [produto](api-management-template-data-model-reference.md#Product) entidades.|Os produtos visíveis ao utilizador atual.|  
  
### <a name="sample-template-data"></a>Dados de exemplo do modelo  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="Product"></a>Produto  
 O **produto** modelo permite-lhe personalizar o corpo da página de produto no portal do programador.  
  
 ![Página de produto do portal de programador](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Modelo predefinido  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Controlos  
 O `Product list` modelo pode utilizar o seguinte [página controlos](api-management-page-controls.md).  
  
-   [botão subscrever](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Produto|[Produto](api-management-template-data-model-reference.md#Product)|O produto especificado.|  
|IsDeveloperSubscribed|Valor booleano|Indica se o utilizador atual está subscrito a este produto.|  
|SubscriptionState|Número|O estado da subscrição. Estados possíveis são:<br /><br /> -   `0 - suspended`– a subscrição está bloqueada e o subscritor não é possível chamar as APIs do produto.<br />-   `1 - active`– a subscrição está ativa.<br />-   `2 - expired`– a subscrição atingiu a respetiva data de expiração e foi desativada.<br />-   `3 - submitted`– o pedido de subscrição foi efetuado pelo programador, mas ainda não foi aprovado ou rejeitado.<br />-   `4 - rejected`– o pedido de subscrição foi negado por um administrador.<br />-   `5 - cancelled`– a subscrição foi cancelada pelo administrador ou programador.|  
|Limites|Matriz|Esta propriedade foi preterida e não deve ser utilizada.|  
|DelegatedSubscriptionEnabled|Valor booleano|Se [delegação](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) está ativada para esta subscrição.|  
|DelegatedSubscriptionUrl|Cadeia|Se a delegação estiver ativada, o URL de subscrição de delegado.|  
|IsAgreed|Valor booleano|Se o produto tiver termos, se o utilizador atual tem Aceito os termos de licenciamento.|  
|Subscrições|Coleção de [resumo de subscrição](api-management-template-data-model-reference.md#SubscriptionSummary) entidades.|As subscrições para o produto.|  
|APIs|Coleção de [API](api-management-template-data-model-reference.md#API) entidades.|As APIs este produto.|  
|CannotAddBecauseSubscriptionNumberLimitReached|Valor booleano|Indica se o utilizador atual é elegível para subscrever este produto no que se refere o limite de subscrição.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|Valor booleano|Indica se o utilizador atual é elegível para subscrever este produto no que se refere a várias subscrições que está a ser permitida ou não.|  
  
### <a name="sample-template-data"></a>Dados de exemplo do modelo  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](api-management-developer-portal-templates.md).