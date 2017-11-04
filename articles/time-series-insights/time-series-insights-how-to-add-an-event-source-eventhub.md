---
title: "Como adicionar uma origem de evento do Hub de eventos para o seu ambiente de informações de séries de tempo do Azure | Microsoft Docs"
description: "Este tutorial aborda como adicionar uma origem de evento que está ligada a um Hub de eventos para o seu ambiente de informações de séries de tempo"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-event-hub-event-source"></a>Como adicionar uma origem de evento do Hub de eventos

Este tutorial abrange como utilizar o portal do Azure para adicionar uma origem de evento que lê a partir de um Hub de eventos para o seu ambiente de informações de séries de tempo.

## <a name="prerequisites"></a>Pré-requisitos

Criar um Hub de eventos e estiver a escrever eventos ao mesmo. Para obter mais informações sobre os Event Hubs, consulte <https://azure.microsoft.com/services/event-hubs/>

> [Grupos de consumidores] Cada origem de evento Insights de séries de tempo tem de ter o seu próprio grupo de consumidores dedicado que não seja partilhado com quaisquer outros consumidores. Se vários leitores consumam eventos a partir do mesmo grupo de consumidores, todos os leitores serão provável que consulte falhas. Tenha em atenção o que há também um limite de 20 grupos de consumidores do Hub de eventos. Para obter mais informações, consulte o [guia de programação de Hubs de eventos](../event-hubs/event-hubs-programming-guide.md).

## <a name="choose-an-import-option"></a>Escolher uma opção de importar

As definições para a origem do evento podem ser introduzidas manualmente ou um hub de eventos pode ser selecionado a partir de hubs de eventos que estão disponíveis para si.
No **opção de importar** Seletor, escolha uma das seguintes opções:

* Fornecer definições de Hub de eventos manualmente
* Utilize o Hub de eventos de subscrições disponíveis

### <a name="select-an-available-event-hub"></a>Selecione um Hub de eventos disponível

A tabela seguinte explica cada opção no separador da nova origem de evento com a respetiva descrição ao selecionar um Hub de eventos disponível como uma origem de evento:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
| --- | --- |
| Nome da origem de evento | O nome da sua origem de evento. Este nome tem de ser exclusivo no seu ambiente de informações de séries de tempo.
| Origem | Escolha **Hub de eventos** para criar uma origem de evento do Hub de eventos.
| Id de subscrição | Selecione a subscrição na qual este hub de eventos foi criado.
| Espaço de nomes de barramento de serviço | Selecione o espaço de nomes de barramento de serviço que contém o Hub de eventos.
| Nome do hub de eventos | Selecione o nome do Hub de eventos.
| Nome de política do hub de eventos | Selecione a política de acesso partilhado, que pode ser criada no separador Configurar do Hub de eventos. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. A política de acesso partilhado para a origem de evento *tem* ter **ler** permissões.
| Grupo de consumidores do hub de eventos | O grupo de consumidores para ler eventos do Hub de eventos. Recomenda-se vivamente a utilizar um grupo de consumidores dedicado para a origem de evento.

### <a name="provide-event-hub-settings-manually"></a>Fornecer definições de Hub de eventos manualmente

A tabela seguinte explica cada propriedade no separador da nova origem de evento com a respetiva descrição ao introduzir as definições manualmente:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
| --- | --- |
| Nome da origem de evento | O nome da sua origem de evento. Este nome tem de ser exclusivo no seu ambiente de informações de séries de tempo.
| Origem | Escolha **Hub de eventos** para criar uma origem de evento do Hub de eventos.
| Id de subscrição | A subscrição na qual este hub de eventos foi criado.
| Grupo de recursos | A subscrição na qual este hub de eventos foi criado.
| Espaço de nomes de barramento de serviço | Um espaço de nomes do Service Bus é um contentor para um conjunto de entidades de mensagens. Quando tiver criado um novo Hub de eventos, também criou um espaço de nomes do Service Bus.
| Nome do hub de eventos | O nome do seu Hub de eventos. Quando criou o seu hub de eventos, também lhe atribuiu um nome específico
| Nome de política do hub de eventos | A política de acesso partilhado, que pode ser criada no separador Configurar do Hub de eventos. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. A política de acesso partilhado para a origem de evento *tem* ter **ler** permissões.
| Chave de política de hub de eventos | A chave de acesso partilhado utilizada para autenticar o acesso ao espaço de nomes do Service Bus. Escreva a chave primária ou secundária aqui.
| Grupo de consumidores do hub de eventos | O grupo de consumidores para ler eventos do Hub de eventos. Recomenda-se vivamente a utilizar um grupo de consumidores dedicado para a origem de evento.

## <a name="next-steps"></a>Passos seguintes

1. Adicionar uma política de acesso de dados ao seu ambiente [dados definir políticas de acesso](time-series-insights-data-access.md)
1. Aceder ao seu ambiente no [Portal de informações de séries de tempo](https://insights.timeseries.azure.com)