---
title: "Controlos de página de API Management do Azure | Microsoft Docs"
description: "Saiba mais sobre os controlos de página disponíveis para utilização em modelos de portal do programador na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 03e0ac8d-64ff-4e9a-b029-d7be14fb31e3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 6aa7a25a9addceee78abe027fb3a19351940464e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-page-controls"></a>Controlos de página de API Management do Azure
Gestão de API do Azure fornece os seguintes controlos para utilização no programador modelos portais.  
  
 Para utilizar um controlo, coloque-o na localização pretendida no modelo de portal de programador. Algumas controla, como o [ações de aplicação](#app-actions) controlar, ter parâmetros, conforme mostrado no exemplo seguinte.  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Os valores para os parâmetros são transmitidos como parte do modelo de dados para o modelo. Na maioria dos casos, pode simplesmente colar o exemplo fornecido para cada controlo para que esta funcione corretamente. Para obter mais informações sobre os valores de parâmetros, pode ver a secção de modelo de dados para cada modelo no qual um controlo pode ser utilizado.  
  
 Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Controlos de página de modelo do portal do Programador  
  
-   [ações de aplicações](#app-actions)  
  
-   [início de sessão básica](#basic-signin)  
  
-   [controlo de paginação](#paging-control)  
  
-   [fornecedores](#providers)  
  
-   [controlo de procura](#search-control)  
  
-   [inscrição](#sign-up)  
  
-   [botão subscrever](#subscribe-button)  
  
-   [Cancelar subscrição](#subscription-cancel)  
  
##  <a name="app-actions"></a>ações de aplicações  
 O `app-actions` controlo fornece uma interface de utilizador para interagir com aplicações na página de perfil do utilizador no portal do programador.  
  
 ![aplicação &#45; controlo ações](./media/api-management-page-controls/APIM-app-actions-control.png "controlo de aplicação ações APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|AppId|O id da aplicação.|  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `app-actions` controlo pode ser utilizado nos seguintes modelos de portal de programador.  
  
-   [Aplicações](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a>início de sessão básica  
 O `basic-signin` controlo fornece um controlo para a recolha de informações na página no portal do Programador de início de sessão de início de sessão de utilizador.  
  
 ![Basic &#45; o controlo de início de sessão](./media/api-management-page-controls/APIM-basic-signin-control.png "controlo de início de sessão básica APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parâmetros  
 nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `basic-signin` controlo pode ser utilizado nos seguintes modelos de portal de programador.  
  
-   [A iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a>controlo de paginação  
 O `paging-control` fornece a funcionalidade de paginação no programador as páginas do portal que apresentam uma lista de itens.  
  
 ![controlo de paginação](./media/api-management-page-controls/APIM-paging-control.png "controlo de paginação APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `paging-control` controlo pode ser utilizado nos seguintes modelos de portal de programador.  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Lista de problema](api-management-issue-templates.md#IssueList)  
  
-   [Lista de produto](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a>fornecedores  
 O `providers` controlo fornece um controlo de seleção de fornecedores de autenticação na página no portal do Programador de início de sessão.  
  
 ![controlo de fornecedores](./media/api-management-page-controls/APIM-providers-control.png "controlo de fornecedores APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parâmetros  
 nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `providers` controlo pode ser utilizado nos seguintes modelos de portal de programador.  
  
-   [A iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a>controlo de procura  
 O `search-control` fornece funcionalidade de pesquisa no programador as páginas do portal que apresentam uma lista de itens.  
  
 ![Procurar controlo](./media/api-management-page-controls/APIM-search-control.png "controlo de procura APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `search-control` controlo pode ser utilizado nos seguintes modelos de portal de programador.  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Lista de produto](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a>inscrição  
 O `sign-up` controlo fornece um controlo para recolher informações de perfil de utilizador na página no portal do programador a inscrição.  
  
 ![início de sessão &#45; até o controlo](./media/api-management-page-controls/APIM-sign-up-control.png "controlo APIM de inscrição")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parâmetros  
 nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `sign-up` controlo pode ser utilizado nos seguintes modelos de portal de programador.  
  
-   [Inscrever-se](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a>botão subscrever  
 O `subscribe-button` fornece um controlo para subscrever um utilizador para um produto.  
  
 ![subscrever &#45; o controlo de botão](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM subscrever-controlo de botão")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parâmetros  
 nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `subscribe-button` controlo pode ser utilizado nos seguintes modelos de portal de programador.  
  
-   [Produto](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a>Cancelar subscrição  
 O `subscription-cancel` controlo fornece um controlo para cancelar uma subscrição de um produto na página de perfil de utilizador no portal do programador.  
  
 ![subscrição &#45; Cancelar controlo](./media/api-management-page-controls/APIM-subscription-cancel-control.png "controlo de subscrição cancelar APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|subscriptionId|O id da subscrição para cancelar.|  
|cancelUrl|O URL de cancelamento da subscrição.|  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `subscription-cancel` controlo pode ser utilizado nos seguintes modelos de portal de programador.  
  
-   [Produto](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de API Management utilizando modelos](api-management-developer-portal-templates.md).