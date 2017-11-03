---
title: Importar uma API na API Management do Azure | Microsoft Docs
description: "Saiba como importar uma API e as suas operações para API Management do Azure."
services: api-management
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: cc56c9a91979ec2ec06b2d63a22b2ded68c0dce1
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Como importar a definição de uma API com operações em API Management do Azure
Na API Management, podem ser criadas novas APIs e operações adicionadas manualmente ou a API pode ser importado juntamente com as operações num único passo.

APIs e as respetivas operações podem ser importadas utilizando os seguintes formatos.

* WADL
* Swagger

Este guia mostra como criar uma nova API e importar as respetivas operações num único passo. Para informações sobre como criar manualmente uma API e adicionar operações, consulte [como criar APIs] [ How to create APIs] e [como adicionar operações a uma API][How to add operations to an API].

## <a name="import-api"> </a>Importar uma API
As APIs são criadas e configuradas no portal do publicador. Para aceder ao portal do publicador, clique em **portal do publicador** no Portal do Azure para o seu serviço de API Management. Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].

![Portal do publicador][api-management-management-console]

Clique em **APIs** do **API Management** menu à esquerda e, em seguida, clique em **importar API**.

![API de importação][api-management-import-apis]

O **API de importação** janela tem três separadores que correspondem a três formas para fornecer a especificação de API.

* **Na área de transferência** permite-lhe cole a especificação de API a caixa de texto designado.
* **Ficheiro** permite-lhe procurar e selecione o ficheiro que contém a especificação de API.
* **Partir do URL** permite-lhe fornecer o URL para a especificação da API.

![Formato de importar API][api-management-import-api-clipboard]

Depois de fornecer a especificação de API, utilize os botões de opção no lado direito para indicar o formato de especificação. Os seguintes formatos são suportados.

* WADL
* Swagger

Em seguida, introduza um **sufixo do URL da API Web**. Isto é acrescentado para o URL de base para o seu serviço de gestão de API. A base de URL é comum para todos os APIs alojadas em cada instância de um serviço de API Management. Gestão de API distingue APIs pelo respetivo sufixo e, por conseguinte, o sufixo tem de ser exclusivo para cada API numa instância de serviço de gestão de API específica.

Depois de todos os valores são introduzidos, clique em **guardar** para criar a API e as operações associadas. 

> [!NOTE]
> Para obter um tutorial importar uma API de calculadora básica no formato Swagger, consulte [gerir a sua primeira API na API Management do Azure](api-management-get-started.md).
> 
> 

## <a name="export-api"></a> Exportar uma API
Para além das novas APIs de importação, pode exportar as definições das suas APIs do portal do publicador. Para tal, clique em **exportar API** do **separador Resumo** do seu **API**.

![Exportação de API][api-management-export-api]

APIs podem ser exportadas utilizando WADL ou Swagger. Selecione o formato pretendido, clique em **guardar**e escolha a localização na qual pretende guardar o ficheiro.

![Formato de API de exportação][api-management-export-api-format]

## <a name="next-steps"> </a>Passos seguintes
Depois de uma API é criada e as operações de importação, pode rever e configurar outras definições, adicione a API de um produto e publicá-lo para que fique disponível para programadores. Para obter mais informações, consulte os guias seguintes.

* [Como configurar as definições da API][How to configure API settings]
* [Como criar e publicar um produto][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create APIs]: api-management-howto-create-apis.md
[How to configure API settings]: api-management-howto-create-apis.md#configure-api-settings
