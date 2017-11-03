---
title: Evento em tempo real processamento com o processamento de eventos do Stream Analytics | Microsoft Docs
description: "Saiba como um conjunto de serviços do Azure pode interagir para ativar o processamento de eventos em tempo real e a análise."
keywords: "processamento em tempo real, processamento de eventos, arquitetura de referência"
services: stream-analytics,event-hubs,storage,sql-database
documentationcenter: 
author: samacha
manager: jhubbard
editor: 
ms.assetid: 11af48bc-313c-4527-8c80-91088dc9f3c6
ms.service: stream-analytics
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: samacha
ms.openlocfilehash: a5206f74e61c3cfd210ff80654e31f466ce08977
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitetura de referência: processamento com o Microsoft Azure Stream Analytics de eventos em tempo real
A arquitetura de referência para o Azure Stream Analytics de processamento de eventos em tempo real destina-se para fornecer um blueprint genérico para a implementação de uma plataforma em tempo real como uma solução de processamento de fluxo de serviço (PaaS) com o Microsoft Azure.

## <a name="summary"></a>Resumo
Tradicionalmente, as soluções de análise foi com base nas capacidades, como ETL (extração, transformação, carregamento) e armazenamento de dados, onde os dados são armazenados antes da análise. Os requisitos de alteração, incluindo os dados mais rapidamente atrasados, são enviar este modelo existente para o limite. A capacidade de analisar os dados dentro de mover fluxos antes de armazenamento é uma solução e, embora não seja uma nova capacidade, a abordagem não foram amplamente ADOTOU em todos os verticals da indústria. 

Microsoft Azure fornece um catálogo um vasto conjunto de tecnologias de análise que são capazes de oferecer suporte a uma matriz de requisitos e cenários de solução diferente. Selecionar os serviços do Azure para implementar uma solução ponto-a-ponto, pode ser um desafio dado o leque de ofertas. Este documento foi concebido para descrever as capacidades e interoperacionalidade de vários serviços do Azure que suporta uma solução de transmissão em fluxo de eventos. Também explica alguns dos cenários em que os clientes podem beneficiar deste tipo abordagem.

## <a name="contents"></a>Conteúdos
* Resumo Executivo
* Introdução à análise em tempo real
* Proposta de valor dos dados em tempo real no Azure
* Cenários comuns para análise em tempo real
* Arquitetura e componentes
  * Origens de Dados
  * Camada de integração de dados
  * Camada de análise em tempo real
  * Camada de armazenamento de dados
  * Apresentação / consumo de camada
* Conclusão

**Autor:** Charles Feddersen, Arquiteto de solução, de centro de dados das informações de Excellence, a Microsoft Corporation

**Publicar:** Janeiro de 2015

**Revisão:** 1.0

**Transferir:** [processamento Microsoft Azure Stream Analytics de eventos em tempo real](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

