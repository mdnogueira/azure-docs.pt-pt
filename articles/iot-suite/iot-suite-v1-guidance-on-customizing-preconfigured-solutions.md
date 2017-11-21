---
title: "Personalização de soluções pré-configuradas | Microsoft Docs"
description: "Fornece orientações sobre como personalizar as soluções pré-configuradas do Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: ba965b9bc23b96adb2b1b7c9306cb7f508f820bf
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="customize-a-preconfigured-solution"></a>Personalizar uma solução pré-configurada

As soluções pré-configuradas fornecidas com o Azure IoT Suite demonstram serviços dentro do conjunto de trabalhar em conjunto para fornecer uma solução ponto-a-ponto. A partir deste ponto de partida, existem vários locais que podem expandir e personalizar a solução para cenários específicos. As secções seguintes descrevem estes pontos de personalização comuns.

## <a name="find-the-source-code"></a>Encontrar o código de origem

O código de origem para as soluções pré-configuradas está disponível no GitHub em repositórios do seguintes:

* Monitorização remota: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Manutenção preditiva: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Fábrica ligada: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

O código de origem para as soluções pré-configuradas é fornecido para demonstrar a padrões e práticas utilizados para implementar a funcionalidade de ponto a ponto da solução de IoT utilizando o Azure IoT Suite. Pode encontrar mais informações sobre como criar e implementar as soluções no repositórios do GitHub.

## <a name="change-the-preconfigured-rules"></a>Alterar as regras pré-configuradas

A solução de monitorização remota inclui três [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) tarefas para processar as informações do dispositivo, telemetria e lógica regras na solução.

Os três sequência de tarefas de análise e os respetivos sintaxe são descritos em pormenor no [instruções sobre a solução pré-configurada de monitorização remota](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Pode editar estas tarefas diretamente para a lógica de alterar ou adicionar lógica específica ao seu cenário. Pode encontrar as tarefas do Stream Analytics da seguinte forma:

1. Aceda a [portal do Azure](https://portal.azure.com).
2. Navegue para o grupo de recursos com o mesmo nome que a sua solução de IoT. 
3. Selecione a tarefa de Stream Analytics do Azure que pretende modificar. 
4. Parar a tarefa selecionando **parar** no conjunto de comandos. 
5. Edite a entradas, consulta e saídas.
   
    Uma alteração simple está a mudar a consulta para o **regras** tarefa para utilizar um **"<"** em vez de um **">"**. O portal de solução mostra ainda **">"** quando editar uma regra, mas tenha em atenção a como o comportamento é flipped devido a alteração na tarefa subjacente.
6. Iniciar a tarefa

> [!NOTE]
> O dashboard de monitorização remota depende em dados específicos, para que alterar as tarefas pode fazer com que o dashboard a falhar.

## <a name="add-your-own-rules"></a>Adicionar as suas próprias regras

Para além de alterar as tarefas do Azure Stream Analytics pré-configurada, pode utilizar o portal do Azure para adicionar novas tarefas ou adicionar novas consultas para as tarefas existentes.

## <a name="customize-devices"></a>Personalizar dispositivos

Uma das atividades de extensão mais comuns é a trabalhar com dispositivos específicos ao seu cenário. Existem vários métodos para trabalhar com dispositivos. Estes métodos incluem a alteração de um dispositivo simulado para corresponder ao seu cenário ou utilizando o [SDK do dispositivo IoT] [ IoT Device SDK] para ligar o dispositivo físico para a solução.

Para obter um guia passo a passo para adicionar dispositivos, consulte o [dispositivos de ligação do Iot Suite](iot-suite-v1-connecting-devices.md) artigo e [remoto monitorização C SDK exemplo](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Este exemplo foi concebido para trabalhar com a solução pré-configurada de monitorização remota.

### <a name="create-your-own-simulated-device"></a>Criar o seu próprio dispositivo simulado

Incluído no [código fonte da solução de monitorização remota](https://github.com/Azure/azure-iot-remote-monitoring), está um simulador de .NET. Este simulador é o aprovisionado como parte da solução e pode alterá-la para enviar metadados diferentes, telemetria e responder a comandos diferentes e métodos.

O simulador na solução pré-configurada de monitorização remota pré-configurada simula um dispositivo cooler que emite a telemetria de temperatura e humidade. Pode modificar o simulador no [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projeto quando tiver escolhido o repositório do GitHub.

### <a name="available-locations-for-simulated-devices"></a>Localizações disponíveis para dispositivos simulados

O conjunto predefinido de localizações é em Seattle/Redmond, Washington, Estados Unidos da América. Pode alterar estas localizações no [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Adiciona um processador de atualização de propriedade pretendido para o simulador

Pode definir um valor para uma propriedade pretendido para um dispositivo no portal de solução. É da responsabilidade do dispositivo para lidar com a propriedade de pedido de alteração, quando o dispositivo obtém o valor da propriedade pretendido. Para adicionar suporte para uma alteração de valor de propriedade através de uma propriedade pretendida, terá de adicionar um processador para o simulador.

O simulador contém processadores para a **SetPointTemp** e **TelemetryInterval** propriedades que pode atualizar para atualizar a definição pretendida valores no portal de solução.

O exemplo seguinte mostra o processador para o **SetPointTemp** pretendido propriedade no **CoolerDevice** classe:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Este método atualiza a temperatura do ponto de telemetria e, em seguida, envia um relatório a alteração ao IoT Hub, definindo uma propriedade que relatados.

Pode adicionar os seus próprios processadores para as suas propriedades ao seguir o padrão no exemplo anterior.

Tem também de vincular a propriedade pretendida para o processador conforme mostrado no exemplo seguinte do **CoolerDevice** construtor:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Tenha em atenção que **SetPointTempPropertyName** é uma constante definida como "Config.SetPointTemp".

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Adicionar suporte para um novo método para o simulador

Pode personalizar o simulador para adicionar suporte para um novo [método (method direta)][lnk-direct-methods]. Existem dois passos principais necessários:

- O simulador tem de notificar o IoT hub na solução pré-configurada com detalhes do método.
- O simulador têm de incluir o código para processar a chamada de método quando invocar-à partir de **detalhes do dispositivo** painel no Explorador de soluções ou através de uma tarefa.

Monitorização remota pré-configurada solução utiliza *comunicadas propriedades* enviar detalhes dos métodos suportados ao IoT hub. O solução de back-end mantém uma lista de todos os métodos suportados por cada dispositivo, juntamente com um histórico de invocações de método. Pode ver estas informações sobre os dispositivos e invocar métodos no portal de solução.

Para notificar o IoT hub que um dispositivo suporta um método, o dispositivo tem de adicionar detalhes do método para o **SupportedMethods** nó nas propriedades de comunicado:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

A assinatura de método tem o seguinte formato: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Por exemplo, para especificar o **InitiateFirmwareUpdate** método espera um parâmetro de cadeia denominado **FwPackageURI**, utilize a assinatura de método seguinte:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Para obter uma lista de tipos de parâmetros suportados, consulte o **CommandTypes** classe no projeto de infraestrutura.

Para eliminar um método, defina a assinatura de método para `null` nas propriedades de comunicados.

> [!NOTE]
> O solução de back-end apenas as atualizações de informações sobre métodos suportados quando recebe um *informações do dispositivo* mensagens do dispositivo.

O seguinte exemplo de código do **SampleDeviceFactory** classe no projeto comuns mostra como adicionar um método à lista de **SupportedMethods** nas propriedades de comunicado enviadas pelo dispositivo:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Este fragmento de código adiciona detalhes sobre o **InitiateFirmwareUpdate** método, incluindo o texto a apresentar no portal de solução e detalhes dos parâmetros de método necessário.

O simulador envia comunicadas propriedades, incluindo a lista de métodos suportados, ao IoT Hub quando inicia o simulador.

Adiciona um processador para o código do simulador para cada método suporta. Pode ver os processadores existentes no **CoolerDevice** classe no projeto Simulator.WebJob. O exemplo seguinte mostra o processador para **InitiateFirmwareUpdate** método:

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

Os nomes de processador de método tem de começar com `On` seguido do nome do método. O **methodRequest** parâmetro contém quaisquer parâmetros transmitidos com a invocação do método de solução de back-end. O valor de retorno tem de ser do tipo **tarefas&lt;MethodResponse&gt;**. O **BuildMethodResponse** método do utilitário ajuda-o a criar o valor de retorno.

Dentro do processador, o método foi:

- Inicie uma tarefa assíncrona.
- Obter propriedades pretendidas a partir do *dispositivo duplo* no IoT Hub.
- Atualizar uma única propriedade comunicada utilizando o **SetReportedPropertyAsync** método o **CoolerDevice** classe.
- Atualizar várias propriedades comunicadas criando um **TwinCollection** instância e chamar a **Transport.UpdateReportedPropertiesAsync** método.

O exemplo de atualização de firmware anterior efetua os seguintes passos:

- Verifica que o dispositivo é capaz de aceitar o pedido de atualização de firmware.
- No modo assíncrono inicia a operação de atualização de firmware e repõe a telemetria quando a operação foi concluída.
- Devolve imediatamente a mensagem "FirmwareUpdate aceites", para indicar que o pedido foi aceite pelo dispositivo.

### <a name="build-and-use-your-own-physical-device"></a>Criar e utilizar o seu próprio dispositivo (físico)

O [SDKs IoT do Azure](https://github.com/Azure/azure-iot-sdks) fornecer bibliotecas para ligar a vários tipos de dispositivos (idiomas e sistemas operativos) para soluções de IoT.

## <a name="modify-dashboard-limits"></a>Modificar os limites de dashboard

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Número de dispositivos apresentados na lista pendente de dashboard

A predefinição é 200. Pode alterar este número num [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Número de pins a apresentar no controlo de mapa do Bing

A predefinição é 200. Pode alterar este número num [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Período de tempo do gráfico de telemetria

A predefinição é 10 minutos. Pode alterar este valor na [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Comentários

Tem a personalização gostaria de ver abrangidas neste documento? Adicionar sugestões de funcionalidades para [voz do utilizador](https://feedback.azure.com/forums/321918-azure-iot), ou comentário neste artigo. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as opções para personalizar as soluções pré-configuradas, consulte:

* [Ligar aplicação lógica à sua solução do Azure IoT Suite monitorização remota pré-configurada][lnk-logicapp]
* [Utilizar telemetria dinâmica com a solução pré-configurada de monitorização remota][lnk-dynamic]
* [Metadados de informações de dispositivos na solução pré-configurada de monitorização remota][lnk-devinfo]
* [Personalizar a forma como a solução de fábrica ligado apresenta dados dos seus servidores de OPC UA][lnk-cf-customize]

[lnk-logicapp]: iot-suite-v1-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]: iot-suite-connected-factory-customize.md