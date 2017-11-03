---
title: Gerir zonas DNS no DNS do Azure - portal do Azure | Microsoft Docs
description: "Pode gerir zonas DNS através do portal do Azure. Este artigo descreve como atualizar, eliminar e criar zonas DNS no DNS do Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: gwallace
ms.openlocfilehash: 69a509612e6204fc93dd42bf2fe69cb165b5777c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Como gerir zonas DNS no portal do Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI do Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra como gerir as zonas DNS através do portal do Azure. Também pode gerir as zonas DNS com a plataforma [CLI do Azure](dns-operations-dnszones-cli.md) ou do Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
2. No menu Hub, clique em **Novo > Redes >** e, em seguida, clique em **Zona DNS**, para abrir o painel Criar zona DNS.

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:


   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|contoso.com|O nome da zona DNS|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar a zona DNS.|
   |**Grupo de recursos**|**Criar novo:** contosoDNSRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Localização**|EUA Oeste||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

## <a name="list-dns-zones"></a>Zonas DNS de lista

No portal do Azure, navegue para **mais serviços** > **redes** > **zonas DNS**. Cada zona DNS é é seus próprios recursos, informações como o número de conjuntos de registos e servidores de nomes são visíveis desta vista. A coluna **servidores de nomes** não se encontra na vista predefinida, adicioná-lo clique **colunas**, selecione **nome servidores** e clique em **feito**.

![listar zonas DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

Navegue para uma zona DNS no portal. No **zona DNS** painel, clique em **eliminar zona**. Lhe for pedido para confirmar que está a intenção eliminar a zona DNS. Também eliminar uma zona DNS elimina todos os registos estão contidos na zona.

## <a name="next-steps"></a>Passos seguintes

Saiba como trabalhar com a sua zona DNS e os registos, visitando [introdução ao DNS do Azure no portal do Azure](dns-getstarted-portal.md).