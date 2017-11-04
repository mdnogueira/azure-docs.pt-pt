---
title: "Implementar serviços da API Management do Azure em várias regiões do Azure | Microsoft Docs"
description: "Saiba como implementar uma instância de serviço de API Management do Azure em várias regiões do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 47389ad6-f865-4706-833f-846115e22e4d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 75a0fd7f02bcc7ed5278582f36d0ed6d54aa7bc4
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Como implementar uma instância de serviço de API Management do Azure em várias regiões do Azure
Gestão de API suporta a implementação de multirregião que permite que os publicadores de API distribuir um único serviço de gestão de API em qualquer número de regiões do Azure pretendidos. Isto ajuda a reduzir o pedido de latência interpretada pela distribuída geograficamente os consumidores de API e melhora também a disponibilidade do serviço se uma região ficar offline. 

Quando um serviço de API Management é criado inicialmente, contém apenas um [unidade] [ unit] e reside numa única região do Azure, que é designada como a região primária. Regiões adicionais podem ser facilmente adicionadas através do Portal do Azure. É implementado um servidor de gateway de gestão de API para cada região e o tráfego de chamada será encaminhado para o gateway mais próximo. Se uma região ficar offline, o tráfego é automaticamente novamente direcionado para o gateway mais próximo seguinte. 

> [!IMPORTANT]
> Implementação de multirregião só está disponível no  **[Premium] [ Premium]**  camada.
> 
> 

## <a name="add-region"></a>Implementar uma instância de serviço de API Management para uma região de novo
> [!NOTE]
> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

No Portal do Azure, navegue para o **escala e preços** página para a instância de serviço de API Management. 

![Separador Escala][api-management-scale-service]

Para implementar uma nova região, clique em **+ adicionar região** da barra de ferramentas.

![Adicionar região][api-management-add-region]

Selecione a localização da lista pendente e defina o número de unidades para com o controlo de deslize.

![Especifique unidades][api-management-select-location-units]

Clique em **adicionar** colocar a sua seleção na tabela de localizações. 

Repita este processo até ter todas as localizações configuradas e clique em **guardar** da barra de ferramentas para iniciar o processo de implementação.

## <a name="remove-region"></a>Eliminar uma instância de serviço de API Management a partir de uma localização
No Portal do Azure, navegue para o **escala e preços** página para a instância de serviço de API Management. 

![Separador Escala][api-management-scale-service]

Para a localização que pretende remover a abrir o menu de contexto utilizando o **...**  botão na extremidade direita da tabela. Selecione o **eliminar** opção.

![Remova a região][api-management-remove-region]

Confirme a eliminação e clique em **guardar** para aplicar as alterações.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Get started with Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

