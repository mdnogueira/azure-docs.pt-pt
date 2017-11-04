---
title: "Definições de porta de reencaminhamento do Azure | Microsoft Docs"
description: Detalhes sobre os valores de portas de reencaminhamento do Azure.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 875f00064f94b37ab5efdde54ca3e6cbda779654
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-port-settings"></a>Definições de porta de reencaminhamento do Azure

A tabela seguinte descreve a configuração necessária para os valores de porta para o reencaminhamento do Azure.

## <a name="hybrid-connections"></a>Ligações Híbridas
As ligações híbridas utiliza WebSockets como o mecanismo de transporte subjacente, que utiliza **HTTPS** apenas. 

## <a name="wcf-relays"></a>Reencaminhamentos do WCF
  
|Enlace|Segurança de transporte|Porta|  
|-------------|------------------------|----------|  
|[Classe de BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (cliente)|Sim|HTTPS| 
| |" |Não|HTTP|  
|[Classe de BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (serviço)|Qualquer um dos|9351/HTTP|  
|[Classe de NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (cliente)|Sim|9351/HTTPS|  
||" |Não|9350/HTTP|  
|[Classe de NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (serviço)|Qualquer um dos|9351/HTTP|  
|[Classe de NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (/ serviço de cliente)|Qualquer um dos|9352/5671/HTTP (9352/9353 se híbridas a utilizar)|  
|[Classe de NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (cliente)|Sim|9351/HTTPS|  
||" |Não|9350/HTTP|  
|[Classe de NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (serviço)|Qualquer um dos|9351/HTTP|  
|[Classe de WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (cliente)|Sim|HTTPS|  
||" |Não|HTTP|  
|[Classe de WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (serviço)|Qualquer um dos|9351/HTTP|  
|[Classe de WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (cliente)|Sim|HTTPS|  
||" |Não|HTTP|  
|[Classe de WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (serviço)|Qualquer um dos|9351/HTTP|

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o reencaminhamento do Azure, visite estas ligações:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)