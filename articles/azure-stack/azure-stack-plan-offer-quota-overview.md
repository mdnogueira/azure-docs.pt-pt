---
title: "Pilha plano, oferta, quota e subscrição descrição geral do Azure | Microsoft Docs"
description: "Como um operador da nuvem, pretender compreender os planos de pilha do Azure, ofertas, quotas e subscrições."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/22/2017
ms.author: erikje
ms.openlocfilehash: 083ca2f0a06625810d2f90a682ba0b3110032e60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Plano, oferta, quota e descrição geral da subscrição

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

[Pilha do Azure](azure-stack-poc.md) permite fornecer uma ampla variedade de serviços, como máquinas virtuais, bases de dados, SharePoint, Exchange e até mesmo [itens do Azure Marketplace](azure-stack-marketplace-azure-items.md). Como um operador de pilha do Azure, configure e fornecer esses serviços na pilha do Azure através da utilização de esquemas, ofertas e quotas.

As ofertas de conter um ou mais planos e cada plano inclui um ou mais serviços. Ao criar planos e combiná-los em ofertas diferentes, pode controlar
- podem aceder a que os utilizadores de serviços e recursos
- a quantidade desses recursos que os utilizadores podem consumir
- as regiões que tem acesso a recursos

Quando fornecer um serviço, que terá de seguir estes passos de alto nível:

1. Adicione um serviço que pretende fornecer aos seus utilizadores.
2. Crie um plano que contém um ou mais serviços. Ao criar um plano, terá de selecionar ou criar quotas que definem os limites de recursos de cada serviço no plano.
3. Crie uma oferta que contém um ou mais planos (incluindo planos base e planos de suplemento opcional).

Depois de ter criado a oferta, podem subscrever os seus utilizadores para aceder a serviços e recursos que fornece ao. Os utilizadores podem subscrever para ofertas tantos como pretendem. O diagrama seguinte mostra um exemplo simples de um utilizador que tiver subscrito dois oferece. Cada oferta tem um plano ou dois e cada plano lhes dá acesso aos serviços.

![](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Planos

Planos são agrupamentos de um ou mais serviços. Como um operador de pilha do Azure, [criar planos](azure-stack-create-plan.md) para oferecer aos seus utilizadores. Por sua vez, os utilizadores subscrevem as ofertas para utilizar os serviços incluem e planos. Ao criar planos, certifique-se definir as quotas, definir os esquemas de base e considerar, incluindo planos de suplemento opcional.

### <a name="quotas"></a>Quotas

Para ajudar a gerir a sua capacidade de nuvem, selecione ou crie uma quota para cada serviço num plano. Quotas definem os limites de recursos superior que uma subscrição de utilizador pode aprovisionar ou consumir. Por exemplo, uma quota pode permitir que um utilizador criar até cinco máquinas virtuais. Quotas podem limitar uma variedade de recursos, como máquinas virtuais, CPU e RAM, limites.

Quotas podem ser configuradas por região. Por exemplo, um plano de serviços de computação da região A ter uma quota de duas máquinas virtuais, 4 GB de RAM e 10 núcleos de CPU. No Kit de desenvolvimento de pilha do Azure, apenas uma região (com o nome *local*) está disponível.

### <a name="base-plan"></a>Plano de base

Ao criar uma oferta, o administrador de serviços pode incluir um plano de base. Estes esquemas de base são incluídas por predefinição, quando um utilizador subscreve que oferta. Quando um utilizador subscreve, têm acesso a todos os fornecedores de recursos especificado esses planos base (com as quotas correspondentes).

### <a name="add-on-plans"></a>Planos de suplemento

Também pode incluir planos de suplemento opcional numa oferta. Planos de suplemento não estão incluídos por predefinição na subscrição. Planos de suplemento são planos adicionais (com as quotas) disponíveis numa oferta, que pode adicionar um subscritor para as suas subscrições. Por exemplo, pode oferecer um plano de base com recursos limitados para uma versão de avaliação e um plano de suplemento com recursos mais significativas para os clientes que optar por adotar o serviço.

## <a name="offers"></a>Ofertas

Ofertas são grupos de um ou mais planos que criar para que os utilizadores podem subscrever aos mesmos. Por exemplo, oferecem Alpha pode conter um plano de que contém um conjunto de serviços de computação e planear B, que contém um conjunto de serviços de armazenamento e rede. 

Quando lhe [criar uma oferta](azure-stack-create-offer.md), tem de incluir, pelo menos, um plano de base, mas também pode criar planos de suplemento que podem adicionar utilizadores à sua subscrição.


## <a name="subscriptions"></a>Subscrições

É uma subscrição, como os utilizadores aceder às suas ofertas. Se tiver um operador de pilha do Azure num fornecedor de serviços, os utilizadores (inquilinos) comprar os serviços de ao subscrever as ofertas. Se tiver um operador de pilha do Azure, uma organização, os utilizadores (empregados) podem subscrever os serviços que oferecem sem pagar. Cada combinação de um utilizador com uma oferta é uma subscrição exclusiva. Assim, um utilizador pode ter oferece várias subscrições, mas cada subscrição se aplica apenas uma oferta. Planos, ofertas e quotas aplicam-se apenas a cada subscrição exclusiva – não pode ser partilhadas entre subscrições. Cada recurso que cria um utilizador está associado uma subscrição.


### <a name="default-provider-subscription"></a>Subscrição do fornecedor predefinido

A subscrição do fornecedor predefinido é criada automaticamente quando implementa o Kit de desenvolvimento de pilha do Azure. Esta subscrição pode ser utilizada para gerir a pilha do Azure, implementar mais fornecedores de recursos e criar planos e as ofertas para os utilizadores. Para segurança e as razões de licenciamento, este não deve ser utilizada para executar aplicações e cargas de trabalho do cliente. 

## <a name="next-steps"></a>Passos seguintes

[Criar um plano](azure-stack-create-plan.md)
