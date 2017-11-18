---
title: "Criar um IoT Hub do Azure através de um modelo (PowerShell) | Microsoft Docs"
description: Como utilizar um modelo Azure Resource Manager para criar um IoT Hub com o PowerShell.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 242c50b61b00bbf71b26e2aea1a66e2b2c55dbd5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Criar um hub IoT utilizando o modelo Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pode utilizar o Azure Resource Manager para criar e gerir os hubs IoT do Azure através de programação. Este tutorial mostra como utilizar um modelo Azure Resource Manager para criar um hub IoT com o PowerShell.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação Azure Resource Manager.

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

> [!TIP]
> O artigo [utilizar o Azure PowerShell com o Azure Resource Manager] [ lnk-powershell-arm] fornece mais informações sobre como utilizar o PowerShell e o Azure Resource Manager modelos para criar recursos do Azure.

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Numa linha de comandos do PowerShell, introduza o seguinte comando para iniciar sessão na sua subscrição do Azure:

```powershell
Login-AzureRmAccount
```

Se tiver várias subscrições do Azure, o início de sessão Azure concede acesso a todas as subscrições Azure associadas com as suas credenciais. Utilize o seguinte comando para listar as subscrições do Azure disponíveis para que possa utilizar:

```powershell
Get-AzureRMSubscription
```

Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu IoT hub. Pode utilizar o nome da subscrição ou o ID da saída do comando anterior:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

Pode utilizar os seguintes comandos para detetar onde pode implementar um IoT hub e as versões de API atualmente suportadas:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Crie um grupo de recursos para conter o seu IoT hub, utilizando o seguinte comando das localizações suportadas para o IoT Hub. Este exemplo cria um grupo de recursos denominado **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Submeter um modelo para criar um hub IoT

Utilize um modelo JSON para criar um hub IoT no seu grupo de recursos. Também pode utilizar um modelo Azure Resource Manager para efetuar alterações a um IoT hub.

1. Utilize um editor de texto para criar um modelo Azure Resource Manager chamado **Template** com a seguinte definição do recurso para criar um novo IoT hub padrão. Este exemplo adiciona o IoT Hub a **EUA Leste** região, cria dois grupos de consumidores (**cg1** e **cg2**) no ponto de final compatível com o Event Hub e utiliza o **2016-02-03** versão da API. Este modelo espera também lhe transmitir o nome do hub IoT como um parâmetro denominado **hubName**. Para a atual lista de localizações que suportam o IoT Hub consulte [Azure estado][lnk-status].

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Guarde o ficheiro de modelo Azure Resource Manager no seu computador local. Este exemplo assume que guarde-o numa pasta chamada **c:\templates**.

3. Execute o seguinte comando para implementar o seu novo IoT hub, transmitir o nome do seu IoT hub como um parâmetro. Neste exemplo, o nome do IoT hub é `abcmyiothub`. O nome do seu IoT hub deve ser globalmente exclusivo:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. A saída mostra as chaves para o IoT hub que criou.

5. Para verificar a sua aplicação adicionada novo IoT hub, visite o [portal do Azure] [ lnk-azure-portal] e ver a lista de recursos. Em alternativa, utilize o **Get-AzureRmResource** cmdlet do PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona uma S1 Standard IoT Hub para o qual é-lhe faturado. Pode eliminar o hub IoT através de [portal do Azure] [ lnk-azure-portal] ou utilizando o **remover AzureRmResource** cmdlet do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou um IoT hub utilizando um modelo Azure Resource Manager com o PowerShell, pode querer explorar ainda mais:

* Leia sobre as capacidades do [fornecedor de recursos do IoT Hub REST API][lnk-rest-api].
* Leitura [descrição geral do Azure Resource Manager] [ lnk-azure-rm-overview] para saber mais sobre as funcionalidades do Gestor de recursos do Azure.

Para obter mais informações sobre como desenvolver para o IoT Hub, consulte os artigos seguintes:

* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs IoT do Azure][lnk-sdks]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
