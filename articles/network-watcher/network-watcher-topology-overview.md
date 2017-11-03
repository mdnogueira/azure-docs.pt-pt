---
title: "Introdução à topologia de observador de rede do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral das capacidades de topologia de observador de rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: d5cb5ba431eeae1956a9dbf1d48561c66faef9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-topology-in-azure-network-watcher"></a>Introdução à topologia de observador de rede do Azure

Topologia devolve um gráfico dos recursos de rede numa rede virtual. O gráfico ilustra o interconnection entre os recursos para representar a conectividade de rede ponto a ponto.

![Descrição geral da topologia][1]

No portal, topologia devolve os objetos de recurso um por base de rede virtual. As relações estão representadas por linhas entre os recursos recursos fora da região de observador de rede, mesmo que no recurso de grupo não será apresentado. Os recursos devolvidos na vista de portal são um subconjunto dos componentes de rede que são graphed. Para ver a lista completa dos recursos de rede, pode utilizar [PowerShell](network-watcher-topology-powershell.md) ou [REST](network-watcher-topology-rest.md)

> [!NOTE]
> É necessária uma instância do observador de rede em cada região que pretende executar a topologia de.

Como os recursos são devolvidos a ligação entre eles são modelados em duas relações.

- **Contenção** -exemplo: Rede Virtual contém uma sub-rede que contém um NIC
- **Associados** -exemplo: A NIC está associado uma VM

### <a name="next-steps"></a>Passos seguintes

Saiba como utilizar o PowerShell para obter a vista de topologia, visitando [topologia de observador de rede com o PowerShell](network-watcher-topology-powershell.md)

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png
