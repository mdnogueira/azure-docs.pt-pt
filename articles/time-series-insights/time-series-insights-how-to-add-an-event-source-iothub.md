---
title: "Como adicionar uma origem de evento do IoT Hub para o seu ambiente de informações de séries de tempo do Azure | Microsoft Docs"
description: "Este tutorial aborda como adicionar uma origem de evento que está ligada a um IoT Hub para o seu ambiente de informações de séries de tempo"
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
ms.openlocfilehash: 3b98728ced81256d05b1bed2c92fc66c5ca61b98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-iot-hub-event-source"></a>Como adicionar uma origem de evento do IoT Hub

Este tutorial abrange como utilizar o portal do Azure para adicionar uma origem de evento que lê a partir de um IoT Hub para o seu ambiente de informações de séries de tempo.

## <a name="prerequisites"></a>Pré-requisitos

Criar um IoT Hub e estiver a escrever eventos ao mesmo. Para obter mais informações sobre os Hubs IoT, consulte <https://azure.microsoft.com/services/iot-hub/>

> [Grupos de consumidores] Cada origem de evento Insights de séries de tempo tem de ter o seu próprio grupo de consumidores dedicado que não seja partilhado com quaisquer outros consumidores. Se vários leitores consumam eventos a partir do mesmo grupo de consumidores, todos os leitores serão provável que consulte falhas. Para obter mais informações, consulte o [guia para programadores do IoT Hub](../iot-hub/iot-hub-devguide.md).

## <a name="choose-an-import-option"></a>Escolher uma opção de importar

As definições para a origem do evento podem ser introduzidas manualmente ou pode ser selecionado um IoT hub os IoT hubs que estão disponíveis para si.
No **opção de importar** Seletor, escolha uma das seguintes opções:

* Forneça definições de IoT Hub manualmente
* Utilizar o IoT Hub a partir de subscrições disponíveis

### <a name="select-an-available-iot-hub"></a>Selecione um IoT Hub disponíveis

A tabela seguinte explica cada opção no separador da nova origem de evento com a respetiva descrição ao selecionar um IoT Hub disponível como uma origem de evento:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
| --- | --- |
| Nome da origem de evento | O nome da sua origem de evento. Este nome tem de ser exclusivo no seu ambiente de informações de séries de tempo.
| Origem | Escolha **IoT Hub** para criar uma origem de evento do IoT Hub.
| Id de subscrição | Selecione a subscrição na qual este hub IoT foi criado.
| Nome do hub IoT | Selecione o nome do IoT Hub.
| Nome da política do IoT hub | Selecione a política de acesso partilhado, o que pode ser encontrada no separador de definições do IoT Hub. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. A política de acesso partilhado para a origem de evento *tem* ter **serviço ligar** permissões.
| Grupo de consumidores do hub IoT | O grupo de consumidores para ler eventos do IoT Hub. Recomenda-se vivamente a utilizar um grupo de consumidores dedicado para a origem de evento.

### <a name="provide-iot-hub-settings-manually"></a>Forneça definições de IoT Hub manualmente

A tabela seguinte explica cada propriedade no separador da nova origem de evento com a respetiva descrição ao introduzir as definições manualmente:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
| --- | --- |
| Nome da origem de evento | O nome da sua origem de evento. Este nome tem de ser exclusivo no seu ambiente de informações de séries de tempo.
| Origem | Escolha **IoT Hub** para criar uma origem de evento do IoT Hub.
| Id de subscrição | A subscrição na qual este hub IoT foi criado.
| Grupo de recursos | A subscrição na qual este hub IoT foi criado.
| Nome do hub IoT | O nome do seu IoT Hub. Quando criou o seu hub IoT, também lhe atribuiu um nome específico
| Nome da política do IoT hub | A política de acesso partilhado, que pode ser criada no separador de definições do IoT Hub. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. A política de acesso partilhado para a origem de evento *tem* ter **serviço ligar** permissões.
| Chave de política do IoT hub | A chave de acesso partilhado utilizada para autenticar o acesso ao espaço de nomes do Service Bus. Escreva a chave primária ou secundária aqui.
| Grupo de consumidores do hub IoT | O grupo de consumidores para ler eventos do IoT Hub. Recomenda-se vivamente a utilizar um grupo de consumidores dedicado para a origem de evento.

## <a name="next-steps"></a>Passos seguintes

1. Adicionar uma política de acesso de dados ao seu ambiente [dados definir políticas de acesso](time-series-insights-data-access.md)
1. Aceder ao seu ambiente no [Portal de informações de séries de tempo](https://insights.timeseries.azure.com)