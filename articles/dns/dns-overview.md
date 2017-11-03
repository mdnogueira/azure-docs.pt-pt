---
title: "Descrição geral do DNS do Azure | Microsoft Docs"
description: "Descrição geral do DNS que aloja o serviço no Microsoft Azure. O anfitrião do domínio no Microsoft Azure."
services: dns
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: kumud
ms.openlocfilehash: 890c00f3349abd52294e92d27f1b42ab38fe287a
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-dns-overview"></a>Descrição geral do DNS do Azure

O sistema de nomes de domínio ou DNS, é responsável por traduzir (ou resolver) um nome de Web site ou serviço para o endereço IP. O DNS do Azure é um serviço de alojamento de domínios DNS, fornecer a resolução do nome utilizando a infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure. O DNS do Azure também suporta agora privados domínios DNS. Para obter mais informações, consulte [utilizando o DNS do Azure para domínios privados](private-dns-overview.md).

![Descrição geral do DNS](./media/dns-overview/scenario.png)

## <a name="features"></a>Funcionalidades

* **Fiabilidade e desempenho** -domínios DNS no DNS do Azure estão alojadas na rede de global do Azure dos servidores de nome DNS. O DNS do Azure utiliza Anycast de rede para que cada consulta DNS é respondida pelo servidor DNS disponível mais próximo. Isto fornece desempenho rápido e elevada disponibilidade para o seu domínio.

* **Uma integração perfeita** -serviço DNS do Azure o pode ser utilizado para gerir registos DNS para os serviços do Azure e pode ser utilizado para fornecer o DNS para os seus recursos externos bem. O DNS do Azure está integrado no portal do Azure e utiliza as mesmas credenciais, faturação e contrato do suporte como outros serviços do Azure.

* **Segurança** -serviço do DNS do Azure baseia-se no Azure Resource Manager. Como tal, beneficia das funcionalidades do Gestor de recursos, tais como o controlo de acesso baseado em funções, os registos de auditoria e bloqueio de recurso. Os domínios e registos podem ser geridos através do portal do Azure, os cmdlets do PowerShell do Azure e a CLI do Azure de várias plataformas. As aplicações que requerem gestão automática de DNS podem integrar com o serviço através da REST API e SDKs.

O DNS do Azure não suporta atualmente a compra de nomes de domínio. Se pretende adquirir domínios, terá de utilizar uma entidade de registo de nome de domínio de terceiros. A entidade de registo normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser alojados no DNS do Azure para a gestão de registos DNS. Consulte [delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais detalhes.

## <a name="pricing"></a>Preços

Faturação DNS baseia-se no número de zonas DNS alojadas no Azure e o número de consultas DNS. Para obter mais informações sobre preços visite [preços de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="faq"></a>FAQ

Para perguntas mais frequentes sobre o DNS do Azure, consulte o [FAQ de DNS do Azure](dns-faq.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre registos e zonas DNS, visitando: [DNS zonas e as regista descrição geral](dns-zones-records.md).

Saiba como [criar uma zona DNS](./dns-getstarted-create-dnszone-portal.md) no DNS do Azure.

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.

