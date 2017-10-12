---
title: "Descrição geral de back-ends multi-inquilino com o Gateway de Aplicação do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral do suporte de Gateway de Aplicação para back-ends multi-inquilino."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: c29ff60a50e68c75b4e8f62713d6d1fffd2123d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Suporte de Gateway de Aplicação para back-ends multi-inquilino

O Gateway de Aplicação do Azure suporta conjuntos de dimensionamento de máquinas virtuais, interfaces de rede, IPs público/privado ou nomes de domínios completamente qualificados (FQDN) como parte dos respetivos conjuntos de back-end. Por predefinição, o gateway de aplicação não altera o cabeçalho de anfitrião de HTTP recebido do cliente e envia o cabeçalho inalterado para o back-end. Existem inúmeros serviços, como as [Aplicações Web do Azure](../app-service/app-service-web-overview.md) e a [Gestão de API](../api-management/api-management-key-concepts.md), que são multi-inquilino por natureza e dependem de um cabeçalho de anfitrião específico ou de uma extensão SNI para concretizar a resolução para o ponto final correto. Agora, o Gateway de Aplicação suporta a capacidade de os utilizadores substituírem o cabeçalho de anfitrião de HTTP recebido com base nas definições de HTTP de back-end. Esta capacidade permite o suporte para a gestão de API e aplicações Web do Azure de back-end multi-inquilino. Esta capacidade está disponível tanto para o SKU WAF como para o SKU padrão. O suporte de back-end multi-inquilino também funciona com cenários de SSL ponto a ponto e de terminação de SSL.

![cenário de aplicação Web](./media/application-gateway-web-app-overview/scenario.png)

A capacidade de especificar uma substituição de anfitrião é definida nas definições de HTTP e pode ser aplicada a qualquer conjunto de back-end durante a criação de regras. Os back-ends multi-inquilino suportam as duas formas de substituir o cabeçalho de anfitrião e a extensão SNI que se seguem.

1. Capacidade de definir o nome de anfitrião para um valor fixo nas definições de HTTP. Esta capacidade assegura a substituição do cabeçalho de anfitrião por este valor para todo o tráfego destinado ao conjunto de back-end no qual as definições de HTTP são aplicadas. Ao utilizar o SSL ponto a ponto, este nome de anfitrião substituído é utilizado na extensão SNI. Esta capacidade possibilita cenários nos quais um farm de conjuntos de back-end espera um cabeçalho de anfitrião diferente do cabeçalho de anfitrião de cliente recebido.

2. Capacidade de derivar o nome de anfitrião a partir do IP ou do FQDN dos membros do conjunto de back-end. As definições de HTTP também disponibilizam uma opção que permite escolher o nome de anfitrião a partir do FQDN de um membro do conjunto de back-end, se este estiver configurado com a opção de derivar o nome de anfitrião a partir de um membro do conjunto de back-end individual. Ao utilizar o SSL ponto a ponto, este nome de anfitrião é derivado a partir do FQDN e é utilizado na extensão SNI. Esta capacidade possibilita cenários nos quais um conjunto de back-end pode ter dois ou mais serviços de PaaS multi-inquilino, como as aplicações Web do Azure, e o cabeçalho de anfitrião do pedido para cada membro contém o nome de anfitrião derivado a partir do respetivo FQDN.

> [!NOTE]
> Em ambos os casos descritos atrás, as definições afetam apenas o comportamento do tráfego ativo e não o comportamento da pesquisa do estado de funcionamento. As pesquisas personalizadas já suportam a capacidade de especificar um cabeçalho de anfitrião na configuração da pesquisa. Agora, as pesquisas personalizadas também suportam a capacidade de derivar o comportamento do cabeçalho de anfitrião a partir das definições de HTTP configuradas atualmente. Esta configuração pode ser especificada com o parâmetro `PickHostNameFromback endAddress` na configuração da pesquisa. Para a funcionalidade ponto a ponto funcionar, tanto a pesquisa como as definições de HTTP têm de ser modificadas de modo a refletirem a configuração correta.

Com esta capacidade, os clientes especificam as opções nas definições de HTTP e as pesquisas personalizadas com a configuração adequada. Em seguida, esta definição é associada a um serviço de escuta e a um conjunto de back-end através de uma regra.

## <a name="next-steps"></a>Passos seguintes

Para saber como configurar um gateway de aplicação com uma aplicação Web como um membro do conjunto de back-end, visite a página [Configurar Aplicações Web do Serviço de Aplicações com o Gateway de Aplicação](application-gateway-web-app-powershell.md)
