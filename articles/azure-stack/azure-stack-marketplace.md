---
title: Publicar um item do marketplace personalizado na pilha do Azure (operador da nuvem) | Microsoft Docs
description: Como um operador de pilha do Azure, saiba como publicar um item do marketplace personalizado na pilha do Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.openlocfilehash: 7b5f976eb2d51eb86761a2bd0be6adb45ca87681
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="the-azure-stack-marketplace-overview"></a>Descrição geral do Azure pilha Marketplace

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

O Marketplace é uma coleção de serviços, aplicações e recursos personalizados para a pilha do Azure, como redes, máquinas virtuais, armazenamento e assim sucessivamente. Os utilizadores são fornecidos aqui para criar novos recursos e implementar aplicações de novo. Considerá-lo como um catálogo de compras em que os utilizadores podem procurar e seleccione os itens que pretendem utilizar. Para utilizar um item do Marketplace, os utilizadores tem de subscrever uma oferta que lhe concede acesso ao item.

Como um operador de pilha do Azure, decidir quais os itens a adicionar (publicar) no Marketplace. Pode publicar coisas como bases de dados, serviços aplicacionais e assim sucessivamente. Isto torna-las visíveis para todos os seus utilizadores. Pode publicar itens personalizados que criar. Também pode publicar itens a partir de a crescer [lista de itens do Azure Marketplace](azure-stack-marketplace-azure-items.md). Quando publicar um item no Marketplace, os utilizadores podem vê-lo dentro de cinco minutos.

Para abrir o Marketplace, clique em **novo**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Itens do Marketplace
Um item do Marketplace de pilha do Azure é um serviço, aplicação ou recurso de que os utilizadores podem transferir e utilizar. Todos os itens do Marketplace de pilha do Azure são visíveis para todos os seus utilizadores.

Tem a todos os itens do Marketplace:

* Um modelo Azure Resource Manager para o aprovisionamento de recursos
* Metadados, como a cadeias, ícones e outro collateral marketing
* Informações de formatação para apresentar o item no portal

Todos os itens publicados no Marketplace utiliza um formato denominado pacote da galeria do Azure (azpkg). Adicione implementação ou o tempo de execução recursos (por exemplo, o código, ficheiros zip com software ou imagens da máquina virtual) a pilha do Azure em separado, não como parte do Item do Marketplace. 

## <a name="next-steps"></a>Passos seguintes
[Criar e publicar um item do marketplace](azure-stack-create-and-publish-marketplace-item.md)

