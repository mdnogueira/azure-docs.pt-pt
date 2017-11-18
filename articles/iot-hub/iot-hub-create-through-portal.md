---
title: Utilizar o portal do Azure para criar um IoT Hub | Microsoft Docs
description: "Como criar, gerir e eliminar os hubs IoT do Azure através do portal do Azure. Inclui informações sobre os escalões de preço, dimensionamento, segurança e configuração de mensagens."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: dobett
ms.openlocfilehash: ac1a52355ffa5354bebe3b98fdb75783bcd57697
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Criar um IoT hub no portal do Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo descreve:

* Como localizar o serviço de IoT Hub no portal do Azure.
* Como criar e gerir os hubs IoT.

## <a name="where-to-find-the-iot-hub-service"></a>Onde encontrar o serviço de IoT Hub

Pode encontrar o serviço de IoT Hub nas seguintes localizações no portal:

* Escolha **+ novo**, em seguida, escolha **Internet das coisas**.
* No Marketplace, escolha **Internet das coisas**.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Pode criar um IoT hub, utilizando os seguintes métodos:

* O **+ novo** opção abre painel mostrado na captura de ecrã seguinte. Os passos para criar o hub IoT através deste método e através do marketplace são idênticos.
* No Marketplace, escolha **criar** para abrir o painel mostrado na captura de ecrã seguinte.

As secções seguintes descrevem os vários passos para criar um hub IoT:

### <a name="choose-the-name-of-the-iot-hub"></a>Escolha o nome do IoT hub

Para criar um hub IoT, tem de nome do hub IoT. Este nome tem de ser exclusivo em todos os IoT hubs.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Escolha o escalão de preço

Pode escolher entre quatro camadas: **livres**, **1 padrão** e **2 padrão**, e **Standard S3**. O escalão gratuito permite apenas 500 dispositivos para estar ligado ao IoT hub e até 8.000 mensagens por dia.

**No padrão S1**: utilizar a edição de S1 para soluções de IoT com um grande número de dispositivos que cada gerar pequenas quantidades de dados. Cada unidade da edição S1 permite até 400 000 mensagens por dia em todos os dispositivos ligados.

**No padrão S2**: utilizar a edição de S2 para soluções de IoT no qual os dispositivos gerar grandes quantidades de dados. Cada unidade da edição S2 permite até 6 milhões de mensagens por dia entre todos os dispositivos ligados.

**Standard S3**: utilizar a edição de S3 para soluções de IoT que geram grandes quantidades de dados. Cada unidade da edição S3 permite 300 milhões de mensagens por dia entre todos os dispositivos ligados.

![][4]

> [!NOTE]
> IoT Hub apenas permite um hub gratuito por subscrição do Azure.

### <a name="iot-hub-units"></a>Unidades de hub IoT

O número de mensagens permitidas por unidade por dia depende do escalão de preço do seu hub. Por exemplo, se pretender que o IoT hub para suportar a entrada de 700.000 mensagens, escolha as duas unidades de escalão S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo para partições de nuvem e de grupo de recursos

Pode alterar o número de partições para um hub IoT. O número predefinido de partições é 4, pode escolher um número diferente da lista pendente.

Não é necessário criar um grupo de recursos vazio explicitamente. Quando cria um recurso, pode escolher a criar um novo ou utilizar um grupo de recursos existente.

![][5]

### <a name="choose-subscription"></a>Selecione a subscrição

IoT Hub do Azure lista automaticamente as subscrições do Azure, que a conta de utilizador está ligada. Pode escolher a subscrição do Azure para associar o IoT hub para.

### <a name="choose-the-location"></a>Escolha a localização

A opção de localização fornece uma lista de regiões onde o IoT Hub está disponível.

### <a name="create-the-iot-hub"></a>Criar o hub IoT

Quando todos os passos anteriores estiverem concluídos, pode criar o hub IoT. Clique em **criar** para iniciar o processo de back-end para criar e implementar o IoT hub com as opções que escolheu.

Pode demorar alguns minutos para criar o hub IoT como demora algum tempo para a implementação de back-end para execução nos servidores de localização adequada.

## <a name="change-the-settings-of-the-iot-hub"></a>Alterar as definições do IoT hub

Pode alterar as definições de um IoT hub depois de ser criado a partir do painel do IoT Hub.

![][8]

**Políticas de acesso de partilhado**: estas políticas definem as permissões para dispositivos e serviços para ligar ao IoT Hub. Pode aceder a estas políticas ao clicar em **políticas de acesso partilhado** em **geral**. Neste painel, pode modificar as políticas existentes ou adicionar uma nova política.

### <a name="create-a-policy"></a>Criar uma política

* Clique em **adicionar** para abrir um painel. Aqui pode introduzir o novo nome de política e as permissões que pretende associar esta política, conforme mostrado na figura seguinte:

    Existem várias permissões que podem ser associadas estas políticas partilhadas. O **registo ler** e **escrita registo** políticas conceder direitos de acesso de escrita e leitura para o registo de identidade. Escolher a opção de escrita automaticamente escolhe a opção de leitura.

    O **serviço ligar** política concede permissão para aceder a pontos finais de serviço, tal como **receção dispositivo para nuvem**. O **ligar dispositivo** política concede permissões para enviar e receber mensagens utilizando pontos finais do lado do dispositivo IoT Hub.

* Clique em **criar** para adicionar esta política para a lista existente criada de novo.

![][10]

## <a name="endpoints"></a>Pontos Finais

Clique em **pontos finais** para apresentar uma lista de pontos finais para o IoT hub que está a modificar. Existem dois tipos de pontos finais: pontos finais que estão incorporados no IoT hub e pontos finais que adicionar ao IoT hub após a respetiva criação.

![][11]

### <a name="built-in-endpoints"></a>Pontos finais incorporados

Existem dois pontos finais incorporados: **Cloud a comentários de dispositivo** e **eventos**.

* **Cloud a comentários de dispositivo** definições: esta definição não tem dois subsettings: **nuvem para dispositivo TTL** (time-to-live) e **período de retenção** (em horas) para as mensagens. Quando o primeiro criar um hub IoT, ambas estas definições tem o valor predefinido de uma hora. Para ajustar estas definições, utilize os controlos de deslize ou escreva os valores.
* **Eventos** definições: esta definição não tem vários subsettings, algumas das quais são só de leitura. A lista seguinte descreve estas definições:

  * **As partições**: um valor predefinido é definido quando é criado o IoT hub. Pode alterar o número de partições através desta definição.

  * **Nome compatível com o Hub de eventos e o ponto final**: hub IoT o quando é criado, um Hub de eventos é criado internamente que poderá ser necessário acesso em determinadas circunstâncias. Não é possível personalizar os valores de nome e o ponto final compatível com o Event Hub, mas pode copiá-los ao clicar em **cópia**.

  * **Período de retenção**: definido como um dia, por predefinição, mas poderá alterá-la utilizando na lista pendente. Este valor é em dias para a definição de dispositivo para a nuvem.

  * **Grupos de consumidores**: grupos de consumidores ativar vários leitores permite ler mensagens de forma independente do IoT hub. Cada IoT hub é criado com um grupo de consumidores predefinido. No entanto, pode adicionar ou eliminar grupos de consumidores para os hubs IoT com esta definição.

  > [!NOTE]
  > O grupo de consumidores predefinido não pode ser editado ou eliminado.

### <a name="custom-endpoints"></a>Pontos finais personalizados

Pode adicionar pontos finais personalizados no seu IoT hub através do portal. Do **pontos finais** painel, clique em **adicionar** na parte superior para abrir o **adicionar ponto final** painel. Introduza as informações necessárias, em seguida, clique em **OK**. O ponto final personalizado é agora listado no principal **pontos finais** painel.

![][13]

Pode ler mais sobre os pontos finais personalizados no [referência - pontos finais de hub IoT][lnk-devguide-endpoints].

## <a name="routes"></a>Rotas

Clique em **rotas** para gerir a forma como o IoT Hub dispatches as mensagens do dispositivo para a nuvem.

![][14]

Pode adicionar rotas ao seu IoT hub clicando **adicionar** na parte superior a **rotas*** painel, introduzir as informações necessárias e clicando em **OK**. A rota, em seguida, está listada no principal **rotas** painel. Pode editar uma rota clicando na lista de rotas. Para ativar uma rota, clique na lista de rotas e definir o **ativado** alternar para **desativar**. Para guardar a alteração, clique em **OK** na parte inferior do painel.

![][15]

## <a name="pricing-and-scale"></a>Preço e escala

O preço de um IoT hub pode ser alterado através de **preços** definições, com as seguintes exceções:

* Na implementação atual, um IoT hub com um SKU livre não é possível alterar camadas para um dos SKUs pagos, ou vice versa.
* Só pode existir um IoT hub do escalão gratuito na subscrição do Azure.

![][12]

Pode mover de um mais elevado à camada inferior apenas quando o número de mensagens enviadas nesse dia exceder a quota para a camada inferior. Por exemplo, se o número de mensagens em fila por dia exceder 400,000, em seguida, a camada para o IoT hub pode ser alterada. No entanto, se alterar para o escalão S1 o hub IoT é limitado para esse dia.

## <a name="delete-the-iot-hub"></a>Eliminar o hub IoT

Pode navegar para o IoT hub que pretende eliminar clicando **procurar**e, em seguida, escolha o hub adequado para eliminar. Para eliminar o hub IoT, clique em de **eliminar** no botão abaixo o nome do hub IoT.

## <a name="next-steps"></a>Passos seguintes

Siga estas ligações para saber mais sobre a gestão do Azure IoT Hub:

* [Em massa gerir dispositivos de IoT][lnk-bulk]
* [Métricas de IoT Hub][lnk-metrics]
* [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]
* [Proteger a sua solução de IoT a partir do zero cópias de segurança][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
