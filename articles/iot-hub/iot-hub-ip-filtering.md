---
title: "Filtros de ligação de IP do IoT Hub do Azure | Microsoft Docs"
description: "Como utilizar o IP de filtragem para ligações do bloco de endereços IP específicos ao seu hub IoT do Azure. Pode bloquear ligações a partir de individuais ou intervalos de endereços IP."
services: iot-hub
documentationcenter: 
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: boltean
ms.openlocfilehash: 85f5f044faddd5180f0c19d3f2c235b20f6373d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-ip-filters"></a>Utilizar filtros de IP

A segurança é um aspeto importante de qualquer solução IoT com base no IoT Hub do Azure. Por vezes, terá de especificar explicitamente os endereços IP a partir do qual podem ligar dispositivos como parte da configuração de segurança. O _filtro IP_ funcionalidade permite-lhe configurar regras para rejeitar ou aceitar o tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando utilizar

Existem dois casos de utilização específicos quando é útil bloquear os pontos finais de IoT Hub para determinados endereços IP:

- IoT hub deve receber o tráfego apenas a partir de um especificado intervalo de endereços IP e rejeitar tudo o resto. Por exemplo, estiver a utilizar o IoT hub com [Azure Express Route] para criar ligações privadas entre um IoT hub e a sua infraestrutura no local.
- Terá de rejeitar o tráfego de endereços IP que tenham sido identificados como suspeita pelo administrador de hub IoT.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

São aplicadas as regras de filtro IP ao nível de serviço do IoT Hub. Por conseguinte, as regras de filtro IP se aplicam a todas as ligações de dispositivos e aplicações de back-end utilizando os protocolos suportados.

Qualquer tentativa de ligação de um endereço IP que corresponde a uma regra IP rejecting no seu IoT hub recebe um código de estado 401 não autorizado e a descrição. A mensagem de resposta não menciona a regra IP.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, o **filtro IP** grelha no portal para um hub IoT está vazia. Esta predefinição significa que o seu hub aceita ligações de qualquer endereço IP. Esta predefinição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

![Definições de filtro IP do IoT Hub predefinido][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Quando adicionar uma regra de filtro IP, serão apresentadas para os seguintes valores:

- Um **nome da regra de filtro IP** que tem de ser uma cadeia exclusiva, sensível, alfanumérica até 128 carateres de comprimento. Apenas os ASCII 7 bits carateres alfanuméricos mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` são aceites.
- Selecione um **rejeitar** ou **aceitar** como o **ação** para a regra de filtro IP.
- Forneça um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no CIDR notação 192.168.100.0/22 representa os endereços de IPv4 1024 192.168.100.0 para 192.168.103.255.

![Adicionar uma regra de filtro IP para um hub IoT][img-ip-filter-add-rule]

Depois de guardar a regra, verá um alerta a indicar que a atualização está em curso.

![Notificação sobre como guardar uma regra de filtro IP][img-ip-filter-save-new-rule]

O **adicionar** opção é desativada quando atingir o número máximo de 10 regras do filtro de IP.

Pode editar uma regra existente, fazendo duplo clique na linha que contém a regra.

> [!NOTE]
> Rejeitar IP endereços podem impedir que outros serviços do Azure (por exemplo, o Azure Stream Analytics, Virtual Machines do Azure ou o Explorador do dispositivo no portal) interação com o hub IoT.

> [!WARNING]
> Se utilizar o Azure Stream Analytics (ASA) para ler mensagens a partir de um hub IoT com IP filtragem ativada, utilize o nome compatível com o Event Hub e o ponto final do seu IoT Hub na cadeia de ligação de ASA.

## <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro IP

Para eliminar uma regra de filtro IP, selecione uma ou mais regras na grelha e clique em **eliminar**.

![Eliminar uma regra de filtro de IP do IoT Hub][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

Regras do filtro de IP serão aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se pretender aceitar endereços 192.168.100.0/22 o intervalo e rejeitar tudo o resto, a primeira regra na grelha deve aceitar o endereço intervalo 192.168.100.0/22. A seguinte regra deve rejeitar todos os endereços, utilizando o intervalo 0.0.0.0/0.

Pode alterar a ordem das suas regras de filtro IP na grelha clicando nos três pontos verticais no início de uma linha e através de arrastar e largar.

Para guardar a nova ordem de regra de filtro IP, clique em **guardar**.

![Alterar a ordem das suas regras de filtro de IP do IoT Hub][img-ip-filter-rule-order]

## <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as capacidades do IoT Hub, consulte:

- [Operações de monitorização][lnk-monitor]
- [Métricas de IoT Hub][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure Express Route]:  https://azure.microsoft.com/en-us/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md