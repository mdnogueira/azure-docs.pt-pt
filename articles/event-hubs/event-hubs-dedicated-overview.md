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
ms.openlocfilehash: 613ea691e38b6f0bcd8873fc2ec6bcafb3cc6c78
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
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
| Unidades de débito máximas | 20 (flexível para 100)   | 1 CU≈50 |
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
* Inclui o [capturar](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) funcionalidade dos Event Hubs do Azure, para fornecer a integração com retenção micro batch e a longo prazo
* Zero manutenção: estamos a gerir o balanceamento de carga, SO de atualizações, patches de segurança e a criação de partições.
* Corrigido preço por hora.
* Mensagem retenção cópias de segurança para 7 dias com sem encargos adicionais

Dedicado de Hubs de eventos também remove a algumas das limitações de débito de oferta de padrão. Unidades de débito no escalão Standard entitle é 1000 eventos por segundo ou de 1 MB por segundo de entrada por TU e duplo que quantidade de saída. Contagens de evento de saída e a oferta de escala dedicado tem sem restrições na entrada. Estes limites são regidos apenas pela capacidade de processamento dos hubs de eventos adquiridas.

Este ambiente dedicado reservado fornece outras capacidades exclusivas para esta camada, tais como:

* Controlar o número de espaços de nomes do cluster
* Especifique cada a do débito limitson dos seus espaços de nomes
* Configurar o número de Hubs de eventos em cada espaço de nomes
* Determinar o limite do número de partições

Este serviço é direcionado para os utilizadores de telemetria maiores e está disponível para clientes com um enterprise agreement.

## <a name="how-to-onboard"></a>Como integrar

Pode dimensionar a capacidade ou reduzir verticalmente durante todo o mês para satisfazer as necessidades adicionando ou removendo Comunidade. O plano dedicado é exclusivo na medida em que irá ocorrer uma integração mais prática da equipa de produto do Event Hubs para obter a implementação flexível que é adequada para si. Integrar para este SKU, contacte (suporte à faturação) [https://ms.portal.azure.com/#create/Microsoft.Support] ou o seu representante da Microsoft.

## <a name="next-steps"></a>Passos seguintes
Contacte o seu representante da Microsoft ou Support da Microsoft para obter detalhes adicionais sobre a capacidade de dedicado de Hubs de eventos. Também pode saber mais sobre os Event Hubs, visitando as seguintes ligações:

Para obter informações detalhadas sobre os preços, visite as seguintes ligações:

- [Event Hubs dedicado preços](https://azure.microsoft.com/pricing/details/event-hubs/). Também pode contactar o seu representante da Microsoft ou Support da Microsoft para obter detalhes adicionais sobre a capacidade de dedicado de Hubs de eventos.
- O [FAQ de Hubs de eventos](event-hubs-faq.md) contém informações sobre preços e responde a algumas perguntas frequentes sobre os Event Hubs. 
