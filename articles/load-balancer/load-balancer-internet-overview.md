---
title: "Descrição geral do Balanceador de carga de com acesso à Internet | Microsoft Docs"
description: "Descrição geral para a Internet com o Balanceador de carga e as respetivas funcionalidades. Como um balanceador de carga funciona o Azure com máquinas virtuais e serviços em nuvem."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5b9ffeadf6b1ffc4eaf4f49b85ba752c27da0e46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="internet-facing-load-balancer-overview"></a>Internet destinado ao balanceador descrição geral de carga

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Balanceador de carga do Azure mapeia o público IP endereço e número de porta de tráfego de entrada para o privada IP endereço e número de porta da máquina virtual e vice versa para o tráfego de resposta da máquina virtual. Regras de balanceamento de carga permitem-lhe distribuir tipos específicos de tráfego entre várias máquinas virtuais ou serviços. Por exemplo, pode propagar-se a carga do tráfego de pedido web em vários servidores web ou funções da web.

Para um serviço em nuvem que contém instâncias de funções da web ou funções de trabalho, pode definir um ponto final público no ficheiro de definição (. csdef) do serviço.

O *servicedefinition. csdef* ficheiro contém a configuração de ponto final e quando tiver várias instâncias de função para uma implementação de função web ou de trabalho, o Balanceador de carga será configurada para tal. A forma de adicionar as instâncias para a implementação de nuvem está a alterar a contagem de instâncias no ficheiro de configuração do serviço (. csfg).

## <a name="example-of-an-internet-facing-load-balancer"></a>Exemplo de um balanceador de carga com acesso de Internet

A figura seguinte mostra um ponto final com balanceamento de carga para o tráfego da web que é partilhado entre três máquinas virtuais para a porta TCP pública e privada de 80. Estas três máquinas virtuais estão num conjunto com balanceamento de carga.

![exemplo de Balanceador de carga públicos](./media/load-balancer-internet-overview/IC727496.png)

Figura 1 - ponto final com balanceamento de carga para o tráfego web

Quando os clientes de Internet enviam pedidos de página web para o endereço IP público do serviço de nuvem na porta TCP 80, o Azure Load Balancer distribui os pedidos entre as três máquinas virtuais no conjunto com balanceamento de carga. Para obter mais informações sobre os algoritmos de Balanceador de carga, consulte o [página de descrição geral do Balanceador de carga](load-balancer-overview.md#load-balancer-features).

Por predefinição, o Balanceador de carga do Azure distribui o tráfego de rede equitativamente entre várias instâncias de máquina virtual. Também pode configurar a afinidade de sessão, para obter mais informações, consulte [modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Balanceador de carga interno](load-balancer-internal-overview.md) para compreender melhor o Balanceador de carga é uma melhor opção para a implementação de nuvem.

Também pode [começar a criar um Internet Balanceador de carga com acesso à](load-balancer-get-started-internet-arm-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede de Balanceador de carga específicos.

Se a sua aplicação precisar de manter as ligações ativas para os servidores protegidos por um balanceador de carga, pode compreender melhor as [definições de tempo limite TCP inativo para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Irá ajudá-lo a saber mais sobre o comportamento de ligação inativa quando está a utilizar o Balanceador de Carga do Azure.
