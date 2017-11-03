---
title: "Tráfego vista no Gestor de tráfego do Azure | Microsoft Docs"
description: "Introdução à vista de tráfego do Gestor de tráfego"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: 0a07ff578c6afeedc6f3806d52bfe5aef6945c04
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="traffic-manager-traffic-view"></a>Vista de tráfego do Gestor de tráfego

>[!NOTE]
>A funcionalidade de vista de tráfego no Traffic Manager está em pré-visualização pública e não pode ter o mesmo nível de disponibilidade e fiabilidade como versão de funcionalidades que estão em geral disponibilidade. A funcionalidade não é suportada, pode ter restrita capacidades e poderão não estar disponível em todas as localizações do Azure. Para as notificações mais atualizadas à sua disponibilidade e o estado desta funcionalidade, consulte o [Traffic Manager do Azure atualiza](https://azure.microsoft.com/updates/?product=traffic-manager) página.

Gestor de tráfego fornece-lhe DNS nível encaminhamento para que os utilizadores finais são direcionados para bom estado de funcionamento pontos finais com base no método de encaminhamento que tinha definir quando o perfil ser criado. Isto fornece o Gestor de tráfego com uma vista das suas bases de utilizadores (a um nível de granularidade de resolução de DNs) e os respetivos padrão de tráfego. Quando ativa a vista de tráfego, esta informação é processada para lhe fornecer os conhecimentos acionáveis. 

Ao utilizar a vista de tráfego, pode:
- compreenda onde as bases de utilizadores estão localizadas (até uma local DNS resolução nível granularidade).
- ver o volume de tráfego (observado como consultas DNS, processadas pelo Gestor de tráfego do Azure) provenientes destes regiões.
-  obter informações sobre o que é a latência representativa reportada por estes utilizadores.
- descrição detalhada para os padrões de tráfego específica de cada uma destas bases de utilizadores a regiões do Azure onde tiver pontos finais. 

Por exemplo, pode utilizar a vista de tráfego para compreender as regiões que têm um grande número de tráfego mas sofrem do latências. Em seguida pode utilizar estas informações para planear a expansão de requisitos de espaço para o novo regiões do Azure para que estes utilizadores podem ter uma experiência de latência inferior.

## <a name="how-traffic-view-works"></a>Como funciona a vista de tráfego

Vista de tráfego funciona por meio do Traffic Manager, observe as consultas de entrada recebidas nos últimos sete dias em relação a um perfil que tenha esta funcionalidade ativada. Partir destas informações do utilizador, extrai o IP de origem a resolução DNS, em seguida, é utilizado como uma representação da localização dos utilizadores. Estes são, em seguida, agrupados numa granularidade de nível de resolução DNS para criar regiões de base do utilizador, utilizando as informações geográficas de endereços IP mantida pelo Gestor de tráfego. Gestor de tráfego, em seguida, procura em regiões do Azure para a qual a consulta foi encaminhada e constrói um mapeamento de fluxo de tráfego para os utilizadores dessas regiões.
No próximo passo, Gestor de tráfego está correlacionada com a região de base de utilizador para mapeamento de região do Azure com as tabelas de latência de intelligence rede mantém para redes de outro utilizador final compreender a latência média reportada por utilizadores dessas regiões quando ligar a regiões do Azure. Todos os cálculos estas, em seguida, são apresentados num por DNS resolução IP nível local antes de é apresentada ao utilizador. Pode processar estas informações de um fluxo de trabalho de análise à sua escolha, também pode transferir esta informação como um ficheiro CSV.
Quando utilizar a vista de tráfego, é-lhe cobrada com base no número de pontos de dados utilizado para criar as informações apresentadas. O tipo de ponto de dados apenas utilizado está atualmente as consultas recebidas contra o perfil do Traffic Manager. Para obter mais detalhes sobre os preços, visite o [Gestor de tráfego a página de preços](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Passos seguintes

- Saiba [como funciona o Gestor de tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego
- Saiba como [criar um perfil de Gestor de tráfego](traffic-manager-create-profile.md)

