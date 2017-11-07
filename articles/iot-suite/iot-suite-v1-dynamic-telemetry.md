---
title: "Utilizar telemetria dinâmica | Microsoft Docs"
description: "Siga este tutorial para saber como utilizar telemetria dinâmica com a solução pré-configurada de monitorização remota do Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Utilizar telemetria dinâmica com a solução pré-configurada de monitorização remota

Telemetria dinâmica permite-lhe visualizar a qualquer telemetria enviada para a solução pré-configurada de monitorização remota. Os dispositivos simulados que implementar a solução pré-configurada enviam a telemetria de temperatura e humidade, que pode visualizar o dashboard. Se personalizar dispositivos simulados existentes, criar novos dispositivos simulados ou ligar a dispositivos físicos a solução pré-configurada, pode enviar outros valores de telemetria como a temperatura externa, RPM ou windspeed. Em seguida, pode visualizar esta telemetria adicional no dashboard.

Este tutorial utiliza um dispositivo simulado de Node.js simple que pode modificar facilmente para experimentar a telemetria dinâmica.

Para concluir este tutorial, terá de:

* Uma subscrição ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Azure Free Trial (Avaliação Gratuita do Azure)][lnk_free_trial].
* [NODE.js] [ lnk-node] versão 0.12 ou posterior.

Pode concluir este tutorial em qualquer sistema operativo, tais como o Windows ou Linux, onde pode instalar o Node.js.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Adicionar um tipo de telemetria

O passo seguinte é substituir a telemetria gerada pelo dispositivo simulado Node.js com um novo conjunto de valores:

1. Parar o dispositivo simulado Node.js, escrevendo **Ctrl + C** na sua linha de comandos ou a shell.
2. No ficheiro remote_monitoring.js, pode ver os valores de base de dados para a temperatura existente, a humidade e a telemetria de temperatura externa. Adicione um valor de base de dados para **rpm** da seguinte forma:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. O Node.js simulated dispositivo utiliza o **generateRandomIncrement** função no ficheiro remote_monitoring.js para adicionar um incremento aleatório para os valores de base de dados. Utilize uma ordem aleatória do **rpm** valor através da adição de uma linha de código após os randomizations existentes, da seguinte forma:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Adicione o novo valor de rpm para o payload JSON que o dispositivo envia ao IoT Hub:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Execute o dispositivo simulado do Node.js utilizando o seguinte comando:

    `node remote_monitoring.js`

6. Observe o novo tipo de telemetria RPM que são apresentadas no gráfico no dashboard:

![Adicionar RPM ao dashboard][image3]

> [!NOTE]
> Poderá ter de desativar e, em seguida, ative o dispositivo de Node.js no **dispositivos** página no dashboard para ver imediatamente a alteração.

## <a name="customize-the-dashboard-display"></a>Personalizar a apresentação de dashboard

O **Device-Info** mensagem pode incluir metadados sobre a telemetria do dispositivo pode enviar para o IoT Hub. Estes metadados podem especificar os tipos de telemetria que o dispositivo envia. Modificar o **deviceMetaData** valor no ficheiro remote_monitoring.js para incluir um **telemetria** seguinte definição de **comandos** definição. O seguinte código fragmento mostra o **comandos** definição (Certifique-se de adicionar um `,` depois do **comandos** definição):

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> A solução de monitorização remota utiliza uma correspondência sensível para comparar a definição de metadados com dados no fluxo de telemetria.


Adicionar um **telemetria** definição conforme ilustrado no fragmento de código anterior não altera o comportamento do dashboard. No entanto, os metadados podem também incluir um **DisplayName** atributo para personalizar a apresentação no dashboard. Atualização do **telemetria** definição de metadados, conforme mostrado no seguinte fragmento:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

A seguinte captura de ecrã mostra a forma como esta alteração modifica a legenda do gráfico no dashboard:

![Personalizar a legenda do gráfico][image4]

> [!NOTE]
> Poderá ter de desativar e, em seguida, ative o dispositivo de Node.js no **dispositivos** página no dashboard para ver imediatamente a alteração.

## <a name="filter-the-telemetry-types"></a>Filtrar os tipos de telemetria

Por predefinição, o gráfico no dashboard mostra todas as séries de dados no fluxo de telemetria. Pode utilizar o **Device-Info** metadados para suprimir a apresentação dos tipos de telemetria específico no gráfico. 

Para tornar o gráfico mostra a telemetria de temperatura e humidade apenas, omita **ExternalTemperature** do **Device-Info** **telemetria** metadados da seguinte forma:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

O **temperatura Outdoor** já não é apresentado no gráfico:

![Filtrar a telemetria no dashboard][image5]

Esta alteração afeta apenas a visualização de gráfico. O **ExternalTemperature** valores de dados ainda são armazenados e disponibilizados para qualquer processamento de back-end.

> [!NOTE]
> Poderá ter de desativar e, em seguida, ative o dispositivo de Node.js no **dispositivos** página no dashboard para ver imediatamente a alteração.

## <a name="handle-errors"></a>Processar erros

Para um fluxo de dados apresentar no gráfico, respetivo **tipo** no **Device-Info** metadados tem de corresponder ao tipo de dados dos valores de telemetria. Por exemplo, se os metadados Especifica que o **tipo** humidade dados são **int** e um **duplo** se encontra no fluxo de telemetria, em seguida, a telemetria de humidade não apresentar no gráfico. No entanto, o **humidade** valores ainda são armazenados e disponibilizados para qualquer processamento de back-end.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a utilizar a telemetria dinâmica, pode saber mais sobre como as soluções pré-configuradas utilizam as informações do dispositivo: [solução pré-configurada de monitorização remota os metadados de informações do dispositivo][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
