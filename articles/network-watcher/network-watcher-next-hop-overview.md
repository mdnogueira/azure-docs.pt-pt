---
title: "Introdução ao próximo salto na observador de rede do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral do observador de rede de capacidade de salto seguinte"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bb2ca0486b3b3d27a77b70927cb3cbfbeac12c7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Introdução ao próximo salto na observador de rede do Azure

Tráfego de uma VM é enviado para um destino com base nas rotas efetivas associadas um NIC. Salto seguinte obtém o endereço IP de um pacote e o tipo de próximo salto de uma máquina virtual específica e a NIC. Isto ajuda a determinar se o pacote está a ser direcionado para o destino ou é o tráfego a ser preto holed. Uma configuração incorreta de rotas por utilizador, onde um tráfego é direcionado para uma localização no local ou uma aplicação virtual, pode dar origem a problemas de conectividade. Também o salto seguinte devolve a tabela de rota associada o salto seguinte. Ao consultar um salto seguinte se a rota é definida como uma rota definida pelo utilizador, será devolvida esse rota. Caso contrário, o próximo salto devolve "Rota de sistema".

![Descrição geral de salto seguinte][1]

Segue-se uma lista dos tipos de salto seguintes, que pode ser devolvido ao consultar o salto seguinte.

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Nenhuma

### <a name="next-steps"></a>Passos seguintes

Saiba como utilizar o próximo salto encontrar problemas com conectividade de rede, visitando [verificar o salto seguinte numa VM](network-watcher-check-next-hop-portal.md)

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png













