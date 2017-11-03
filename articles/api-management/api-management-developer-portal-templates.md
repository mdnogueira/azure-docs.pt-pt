---
title: Personalizar o portal do Programador de API Management utilizando modelos-Azure | Microsoft Docs
description: "Saiba como personalizar o portal do Programador de API Management do Azure através de modelos."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 8a2211e76150a90e4e10d79fd527decd3cbcc220
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Como personalizar o portal do Programador de API Management do Azure através de modelos

Existem três formas fundamentais de personalizar o portal do programador na Gestão de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de esquema da página][modify-content-layout]
* [Atualizar os estilos utilizados para elementos de página em todo o portal do programador][customize-styles]
* [Modificar os modelos utilizados para páginas geradas pelo portal] [ portal-templates] (explicado neste guia)

Os modelos são utilizados para personalizar os conteúdos de páginas de portal de programador gerado pelo sistema (por exemplo, documentos de API, produtos, a autenticação de utilizador, etc.). Utilizar [DotLiquid](http://dotliquidmarkup.org/) sintaxe e um conjunto de recursos de cadeia localizada, ícones e controlos de página, fornecido tem uma enorme flexibilidade para configurar o conteúdo das páginas como julgar.

## <a name="developer-portal-templates-overview"></a>Descrição geral de modelos de portal de programador
Editar modelos é feita do **portal do programador** durante a sessão iniciada como administrador. Para aceder à mesma primeiro abrir o Portal do Azure e clique em **portal do publicador** da barra de ferramentas de serviço da sua instância da API Management.

![Portal do publicador][api-management-management-console]

Em seguida, clique em **Portal do programador** no canto superior direito. 

![Menu do portal de programador][api-management-developer-portal-menu]

Para aceder os modelos de portal de programador, clique no ícone de personalizar à esquerda para apresentar o menu de personalização e clique em **modelos**.

![Modelos de portais de programador][api-management-customize-menu]

A lista de modelos apresenta várias categorias de modelos que abrangem as páginas diferentes no portal do programador. Cada modelo é diferente, mas os passos para editá-los e publicar as alterações são os mesmos. Para editar um modelo, clique no nome do modelo.

![Modelos de portais de programador][api-management-templates-menu]

Ao clicar num modelo leva-o para a página de portal de programador é personalizável, pelo que o modelo. Neste exemplo de **lista produto** modelo é apresentado. O **lista produto** modelo controla a área do ecrã indicado pelo retângulo vermelho. 

![Modelo da lista de produtos][api-management-developer-portal-templates-overview]

Alguns modelos, como o **perfil de utilizador** modelos, personalizar as diferentes partes da mesma página. 

![Modelos de perfil de utilizador][api-management-user-profile-templates]

O editor para cada modelo de portal de programador tem duas secções apresentadas na parte inferior da página. O lado esquerdo mostra o painel de edição para o modelo e o lado direito apresenta o modelo de dados para o modelo. 

O modelo de edição painel contém a marcação que controla o aspecto e o comportamento da página correspondente no portal do programador. A marcação no modelo utiliza a [DotLiquid](http://dotliquidmarkup.org/) sintaxe. É um editor popular para DotLiquid [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Todas as alterações efetuadas ao modelo durante a edição são apresentadas em tempo real no browser, mas não estão visíveis para os seus clientes até que tenha [guardar](#to-save-a-template) e [publicar](#to-publish-a-template) o modelo.

![Marcação de modelo][api-management-template]

O **dados modelo** painel fornece um guia para o modelo de dados para as entidades que estão disponíveis para utilização num modelo específico. Este guia fornece ao apresentar os dados em direto que estão atualmente apresentados no portal do programador. Pode expandir os painéis de modelo, clicando no retângulo no canto superior direito do **dados modelo** painel.

![Modelo de dados do modelo][api-management-template-data]

No exemplo anterior, existem dois produtos apresentados no portal do programador que foram obtidos os dados apresentados no **dados modelo** painel, conforme mostrado no exemplo seguinte.

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
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
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

A marcação no **lista produto** modelo processa os dados para fornecer o resultado pretendido, repetir a coleção de produtos para apresentar informações e uma hiperligação para cada produto individuais. Tenha em atenção o `<search-control>` e `<page-control>` elementos na marcação de. Estes controlam a apresentação da pesquisa e controlos na página de paginação. `ProductsStrings|PageTitleProducts`é uma referência de cadeia localizada que contém o `h2` texto do cabeçalho da página. Para obter uma lista de recursos de cadeias, controlos de página e ícones disponíveis para utilização em modelos de portal de programador, consulte [referência de modelos de portal para programadores de API Management](api-management-developer-portal-templates-reference.md).

```html
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

## <a name="to-save-a-template"></a>Para guardar um modelo
Para guardar um modelo, clique em Guardar no editor do modelo.

![Guardar o modelo][api-management-save-template]

As alterações foram guardadas não estão em direto no portal do programador, até serem publicados.

## <a name="to-publish-a-template"></a>Para publicar um modelo
É possível publicar modelos guardados, individualmente ou em todos os conjunto. Para publicar um modelo individuais, clique em publicar no editor do modelo.

![Publicar o modelo][api-management-publish-template]

Clique em **Sim** para confirmar e tornar o modelo em direto no portal de programador.

![Confirmar publicar][api-management-publish-template-confirm]

Para publicar todas as versões de modelo atualmente publicado, clique em **publicar** na lista de modelos. Anular a publicação de modelos são designados por um asterisco seguir o nome do modelo. Neste exemplo, o **lista produto** e **produto** modelos que estão a ser publicados.

![Publicar modelos][api-management-publish-templates]

Clique em **publicar personalizações** para confirmar.

![Confirmar publicar][api-management-publish-customizations]

Modelos publicados recentemente são eficazes imediatamente no portal do programador.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para reverter um modelo para a versão anterior
Para reverter um modelo para a versão publicada anterior, clique em reverter no editor do modelo.

![Reverter o modelo][api-management-revert-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-revert-template-confirm]

A versão publicada anteriormente de um modelo é em direto no portal do programador depois de concluída a operação de reversão.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar um modelo para a versão predefinida
Restaurar modelos para a respetiva versão predefinida é um processo de dois passos. Primeiro tem de ser restaurados os modelos e, em seguida, as versões restauradas tem de ser publicadas.

Para restaurar um único modelo para a versão predefinida, clique em restaurar no editor do modelo.

![Reverter o modelo][api-management-reset-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todos os modelos para as respetivas versões de predefinido, clique em **restaurar modelos predefinidos** da lista de modelo.

![Restaurar os modelos][api-management-restore-templates]

Os modelos restaurados, em seguida, devem ser publicados individualmente ou ao mesmo tempo seguindo os passos no [para publicar um modelo](#to-publish-a-template).

## <a name="next-steps"></a>Passos seguintes
Para informações de referência para modelos de portal de programador, recursos de cadeias, ícones e controlos de página, consulte [referência de modelos de portal para programadores de API Management](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







