---
title: "Utilizar o DNS do Azure com outros serviços do Azure | Microsoft Docs"
description: "Noções sobre como utilizar o DNS do Azure para resolver o nome para outros serviços do Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: gwallace
ms.openlocfilehash: a286508fe445208b6bb348d07434b5722cc3f11e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Como funciona o DNS do Azure com outros serviços do Azure

O DNS do Azure é um alojada DNS gestão e o nome do serviço de resolução. Isto permite-lhe criar nomes DNS públicos para as aplicações e serviços que implementou no Azure. A criação de um nome para um serviço do Azure no seu domínio personalizado é tão simple como adicionar um registo do tipo correto para o seu serviço.

* Para os endereços IP alocados dinamicamente, terá de criar um registo CNAME no DNS que mapeia para o nome DNS Azure criado para o seu serviço. Normas DNS impedem a utilização de um registo CNAME para vértice da zona.
* Para os endereços IP estaticamente atribuídos, pode criar um registo de DNS A utilizar qualquer nome, incluindo um *domínio naked* nome no vértice da zona.

A tabela seguinte descreve os tipos de registo suportado que podem ser utilizados para vários serviços do Azure. Como pode ver desta tabela, o DNS do Azure suporta apenas registos DNS para recursos de rede para a Internet. Não é possível utilizar o DNS do Azure para a resolução do nome de endereços internas e privadas.

| Serviço do Azure | Interface de rede | Descrição |
| --- | --- | --- |
| Gateway de Aplicação |[IP público front-end](dns-custom-domain.md#public-ip-address) |Pode criar um registo de DNS A ou CNAME. |
| Balanceador de Carga |[IP público front-end](dns-custom-domain.md#public-ip-address)  |Pode criar um registo de DNS A ou CNAME. Balanceador de carga pode ter um endereço IP público de IPv6 atribuído dinamicamente. Por conseguinte, tem de criar um registo CNAME para um endereço IPv6. |
| Gestor de Tráfego |Nome público |Só pode criar um CNAME que mapeia para o nome de trafficmanager.net atribuído ao perfil do Traffic Manager. Para obter mais informações, consulte [como Gestor de tráfego funciona](../traffic-manager/traffic-manager-overview.md#traffic-manager-example). |
| O serviço em nuvem |[IP público](dns-custom-domain.md#public-ip-address) |Para os endereços IP estaticamente atribuídos, pode criar um registo DNS. Para os endereços IP alocados dinamicamente, terá de criar um registo CNAME que mapeia para o *cloudapp.net* nome. Esta regra aplica-se para VMs criadas no portal clássico do porque que são implementados como um serviço em nuvem. Para obter mais informações, consulte [configurar um nome de domínio personalizado nos serviços em nuvem](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Serviço de Aplicações | [IP externo](dns-custom-domain.md#app-service-web-apps) |Para endereços IP externos, pode criar um registo DNS. Caso contrário, terá de criar um registo CNAME que mapeia para o nome de azurewebsites.net. Para obter mais informações, consulte [mapear um nome de domínio personalizado para uma aplicação do Azure](../app-service/app-service-web-tutorial-custom-domain.md) |
| VMs do Gestor de recursos |[IP público](dns-custom-domain.md#public-ip-address) |VMs do Gestor de recursos pode ter endereços IP públicos. Uma VM com um endereço IP público também poderá estar atrás de um balanceador de carga. Pode criar um registo DNS A ou CNAME para o endereço público. Este nome personalizado pode ser utilizado para ignorar o VIP no balanceador de carga. |
| VMs clássicas |[IP público](dns-custom-domain.md#public-ip-address) |VMs clássicas criado com o PowerShell ou o CLI pode ser configurada com um endereço de virtual (reservado) dinâmico ou estático. Pode criar um CNAME DNS ou um registo, respetivamente. |
