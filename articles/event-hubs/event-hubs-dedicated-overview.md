---
title: "Descrição geral da capacidade dedicado de Hubs de eventos do Azure | Microsoft Docs"
description: "Descrição geral da capacidade dedicado do Microsoft Azure Event Hubs."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sethm;babanisa
ms.openlocfilehash: db8b119178de0e565b2064e9a52d5e9989d60d38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Descrição geral dos Event Hubs dedicado

*Event Hubs dedicado* capacidade oferece único inquilino implementações para clientes com os requisitos mais demanding. Em escala completa, Event Hubs do Azure pode a entrada mais de 2 milhões de eventos por segundo ou até 2 GB por segundo de telemetria com uma latência de armazenamento e o segundo secundárias totalmente durável. Isto também permite que soluções integradas por lote no mesmo sistema e de processamento em tempo real. Com [capturar os Hubs de eventos](event-hubs-capture-overview.md) incluído na oferta, pode reduzir a complexidade da sua solução, fazendo com que um fluxo único suporta pipelines em tempo real e baseada no batch.

A tabela seguinte compara os escalões de serviço dos Event Hubs. A oferta dedicado de Hubs de eventos é um preço mensal fixo, comparada com a utilização de preço para a maioria das funcionalidades do padrão. A camada dedicada oferece as funcionalidades do plano padrão, mas com capacidade de escala empresarial para os clientes com cargas de trabalho pedir o seu trabalho. 

| Funcionalidade | Standard | Dedicado |
| --- |:---:|:---:|:---:|
| Eventos de entrada | Pagar por milhões de eventos | Incluído |
| Unidade de débito (entrada de 1 MB/seg, saída 2 MB por segundo) | Pagar por hora | Incluído |
| Tamanho da Mensagem | 256 KB | 1 MB |
| Políticas do publicador | Sim | Sim |   
| Grupos de consumidores | 20 | 20 |
| Repetição de mensagens | Sim | Sim |
| Unidades de débito máximas | 20 (flexível para 100)   | 1 CU≈200 |
| Ligações mediadas | 1000 incluídos | 100 K incluído |
| Ligações Mediadas adicionais | Sim | Sim |
| Retenção de Mensagens | 1 dia incluído | Até 7 dias incluídos |
| Captura | Pagar por hora | Incluído |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Vantagens da capacidade dedicado de Hubs de eventos

As seguintes vantagens estão disponíveis quando utilizar Event Hubs dedicado:

* Único inquilino aloja com nenhuma ruído de outros inquilinos.
* Aumenta o tamanho da mensagem para 1 MB por comparação a 256 KB para Standard.
* Desempenho repetíveis sempre.
* Capacidade para satisfazer as necessidades de rajada de garantida.
* Dimensionável entre 1 e unidades de capacidade 8 (CU) – fornecer até 2 milhões de eventos de entrada por segundo.
  * Comunidade gerir o dimensionamento de Event Hubs dedicado, onde cada CU pode fornecer aproximadamente equivalente 200 de unidades de débito (TU).
* Zero manutenção: estamos a gerir o balanceamento de carga, SO de atualizações, patches de segurança e a criação de partições.
* Corrigido mensalmente preços.

Dedicado de Hubs de eventos também remove a algumas das limitações de débito de oferta de padrão. Unidades de débito no escalão Standard entitle é 1000 eventos por segundo ou de 1 MB por segundo de entrada por TU e duplo que quantidade de saída. Contagens de evento de saída e a oferta de escala dedicado tem sem restrições na entrada. Estes limites são regidos apenas pela capacidade de processamento dos hubs de eventos adquiridas.

Este serviço é direcionado para os utilizadores de telemetria maiores e está disponível para clientes com um enterprise agreement.

## <a name="how-to-onboard"></a>Como integrar

A plataforma dedicado de Hubs de eventos é fornecida através de um contrato enterprise com diferentes tamanhos da Comunidade. Cada CU fornece aproximadamente equivalente 200 de unidades de débito. Pode dimensionar a capacidade ou reduzir verticalmente durante todo o mês para satisfazer as necessidades adicionando ou removendo Comunidade. O plano dedicado é exclusivo na medida em que irá ocorrer uma integração mais prática da equipa de produto do Event Hubs para obter a implementação flexível que é adequada para si. 

## <a name="next-steps"></a>Passos seguintes
Contacte o seu representante da Microsoft ou Support da Microsoft para obter detalhes adicionais sobre a capacidade de dedicado de Hubs de eventos. Também pode saber mais sobre os Event Hubs, visitando as seguintes ligações:

Para obter informações detalhadas sobre os preços, visite as seguintes ligações:

- [Event Hubs dedicado preços](https://azure.microsoft.com/pricing/details/event-hubs/). Também pode contactar o seu representante da Microsoft ou Support da Microsoft para obter detalhes adicionais sobre a capacidade de dedicado de Hubs de eventos.
- O [FAQ de Hubs de eventos](event-hubs-faq.md) contém informações sobre preços e responde a algumas perguntas frequentes sobre os Event Hubs. 
