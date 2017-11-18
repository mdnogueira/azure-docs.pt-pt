---
title: Criar um IoT Hub do Azure utilizando um cmdlet do PowerShell | Microsoft Docs
description: Como utilizar um cmdlet do PowerShell para criar um hub IoT.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1d22e1a20d18e06686f893b1f4ae22e6373633b3
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Criar um hub IoT utilizando o cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar os cmdlets do PowerShell do Azure para criar e gerir os hubs IoT do Azure. Este tutorial mostra como criar um hub IoT com o PowerShell.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação Azure Resource Manager.

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [Cmdlets do PowerShell do Azure][lnk-powershell-install].

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

## <a name="create-resource-group"></a>Criar grupo de recursos

Precisa de um grupo de recursos para implementar um IoT hub. Pode utilizar um grupo de recursos existente ou crie um novo.

Pode utilizar o seguinte comando para detetar as localizações onde pode implementar um IoT hub:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Para criar um grupo de recursos para o seu hub IoT das localizações suportadas para o IoT Hub, utilize o seguinte comando. Este exemplo cria um grupo de recursos denominado **MyIoTRG1** no **EUA Leste** região:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Para criar um IoT hub no grupo de recursos que criou no passo anterior, utilize o seguinte comando. Este exemplo cria um **S1** hub chamado **MyTestIoTHub** no **EUA Leste** região:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

O nome do IoT hub têm de ser exclusivo.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Pode listar todos os IoT hubs-na sua subscrição utilizando o seguinte comando:

```powershell
Get-AzureRmIotHub
```

O exemplo anterior adiciona um S1 Standard IoT Hub para o qual é-lhe faturado. Pode eliminar o hub IoT utilizando o seguinte comando:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Em alternativa, pode remover um grupo de recursos e todos os recursos que nele contidos utilizando o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Passos seguintes

Agora que implementou um IoT hub utilizando um cmdlet do PowerShell, pode querer explorar ainda mais:

* Detetar outros [cmdlets PowerShell para trabalhar com o seu IoT hub][lnk-iothub-cmdlets].
* Leia sobre as capacidades do [fornecedor de recursos do IoT Hub REST API][lnk-rest-api].

Para obter mais informações sobre como desenvolver para o IoT Hub, consulte os artigos seguintes:

* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs IoT do Azure][lnk-sdks]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
