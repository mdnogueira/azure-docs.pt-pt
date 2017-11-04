---
title: "Mover uma aplicação Web do Azure para outro grupo de recursos | Microsoft Docs"
description: "Descreve os cenários em que pode mover as aplicações Web e serviços de aplicação de um grupo de recursos para outro."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>Mover uma aplicação Web do Azure para outro grupo de recursos
Pode mover uma aplicação Web e/ou os respetivos recursos relacionados para outro grupo de recursos na mesma subscrição ou para um grupo de recursos numa subscrição diferente. Isto é feito como parte padrão de gestão de recursos no Azure. Para obter mais informações, consulte [recursos do Azure de mover a nova subscrição ou grupo de recursos](../azure-resource-manager/resource-group-move-resources.md).

## <a name="limitations-when-moving-within-the-same-subscription"></a>Limitações ao mover dentro da mesma subscrição

Quando move uma aplicação Web _dentro da mesma subscrição_, não é possível mover os certificados SSL carregados. No entanto, pode mover uma aplicação Web para o novo grupo de recursos sem mover o respetivo certificado SSL carregado e funcionalidade SSL da sua aplicação ainda funciona. 

Se pretender mover o certificado SSL com a aplicação Web, siga estes passos:

1.  Elimine o certificado carregado a partir da aplicação Web.
2.  Mova a aplicação Web.
3.  Carregue o certificado para a aplicação Web movido.

## <a name="limitations-when-moving-across-subscriptions"></a>Limitações ao mover entre subscrições

Quando move uma aplicação Web _entre subscrições_, aplicam as seguintes limitações:

- O grupo de recursos de destino não pode ter quaisquer recursos do serviço de aplicações existentes. Recursos do serviço de aplicações incluem:
    - Aplicações Web
    - Planos do Serviço de Aplicações
    - Certificados SSL carregados ou importados
    - Ambientes do App Service
- Todos os recursos do serviço de aplicações no grupo de recursos têm de ser movidos em conjunto.
- Recursos do serviço de aplicações só podem ser afastados do grupo de recursos no qual foram originalmente criados. Se um recurso de serviço de aplicações já não consta no respetivo grupo de recursos original, têm de ser movido volta a esse grupo de recursos original pela primeira vez e, em seguida, pode ser movida entre subscrições. 
