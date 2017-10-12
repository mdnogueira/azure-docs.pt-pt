---
title: "Como criar um espaço de nomes do Service Bus no portal do Azure | Microsoft Docs"
description: "Crie um espaço de nomes do Service Bus com o portal do Azure."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.openlocfilehash: c8654ed547a9001e2e968d2a45d990a73ef27d3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Criar um espaço de nomes do Service Bus com o portal do Azure

O espaço de nomes é um contentor para todos os componentes de mensagens. Um único espaço de nomes pode conter várias filas e tópicos, sendo que os espaços de nomes servem frequentemente de contentores da aplicação. Existem duas formas diferentes de criar um espaço de nomes do Service Bus:

1. Portal do Azure (este artigo)
2. [Modelos do Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um espaço de nomes no portal do Azure

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Parabéns! Criou um espaço de nomes de Mensagens do Service Bus.

## <a name="next-steps"></a>Passos seguintes

Veja os nossos [exemplos do GitHub][github-samples], que mostram algumas das funcionalidades mais avançadas das mensagens do Service Bus do Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
