---
title: "Apontar um domínio de Internet da empresa para um nome de domínio do Gestor de Tráfego | Microsoft Docs"
description: "Este artigo ajuda-o a apontar o nome de domínio da sua empresa para um nome de domínio do Traffic Manager."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 29822946-2d45-4434-ba47-fc180a445cc3
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 0322b3510cfd4f94031d8c1db8f1cc032b997fa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Apontar um domínio de Internet da empresa para um domínio do Gestor de Tráfego do Azure

Quando cria um perfil do Gestor de Tráfego, o Azure atribui automaticamente um nome DNS a esse perfil. Para utilizar um nome da sua zona DNS, crie um registo DNS CNAME que mapeia para o nome de domínio do seu perfil do Gestor de Tráfego. Pode encontrar o nome de domínio do Gestor de Tráfego na secção **Geral** na página Configuração do perfil do Gestor de Tráfego.

Por exemplo, para apontar o nome www.contoso.com para o nome de DNS do Gestor de Tráfego contoso.trafficmanager.net, crie o seguinte registo de recursos DNS:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Todos os pedidos de tráfego para *www.contoso.com* são direcionados para *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Não é possível apontar um domínio de segundo nível, como *contoso.com*, para o domínio do Traffic Manager. Os padrões de protocolo DNS não permitem registos CNAME para nomes de domínio de segundo nível.

## <a name="next-steps"></a>Passos seguintes

* [Métodos de encaminhamento do Gestor de Tráfego](traffic-manager-routing-methods.md)
* [Gestor de Tráfego – Desativar, ativar ou eliminar um perfil](disable-enable-or-delete-a-profile.md)
* [Gestor de Tráfego – Desativar ou ativar um ponto final](disable-or-enable-an-endpoint.md)
