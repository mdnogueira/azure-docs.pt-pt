---
title: Utilizar o Azure PowerShell para configurar o carregamento de ficheiros | Microsoft Docs
description: "Como utilizar os cmdlets do PowerShell do Azure para configurar o seu IoT hub para ativar o ficheiro de carregamentos de dispositivos ligados. Inclui informações sobre como configurar o conta de armazenamento do Azure de destino."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: c6966fd4a60681643c2a690013035bde20abee78
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configurar o IoT Hub carregamentos de ficheiros com o PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para utilizar o [funcionalidade de carregamento de ficheiros no IoT Hub][lnk-upload], tem primeiro de associar uma conta de armazenamento do Azure com o seu IoT hub. Pode utilizar uma conta de armazenamento existente ou crie um novo.

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [Cmdlets do PowerShell do Azure][lnk-powershell-install].
* Um hub IoT do Azure. Se não tiver um IoT hub, pode utilizar o [cmdlet New-AzureRmIoTHub] [ lnk-powershell-iothub] para criar um ou utilizar o portal para [criar um hub IoT][lnk-portal-hub].
* Uma conta de armazenamento do Azure. Se não tiver uma conta de armazenamento do Azure, pode utilizar o [cmdlets do PowerShell de armazenamento do Azure] [ lnk-powershell-storage] para criar um ou utilizar o portal para [criar uma conta de armazenamento] [lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>A iniciar sessão e definir a sua conta do Azure

Inicie sessão na sua conta do Azure e selecionar a sua subscrição.

1. Na linha de comandos do PowerShell, execute o **Login-AzureRmAccount** cmdlet:

    ```powershell
    Login-AzureRmAccount
    ```

1. Se tiver várias subscrições do Azure, o início de sessão Azure concede acesso a todas as subscrições Azure associadas com as suas credenciais. Utilize o seguinte comando para listar as subscrições do Azure disponíveis para que possa utilizar:

    ```powershell
    Get-AzureRMSubscription
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para gerir o seu IoT hub. Pode utilizar o nome da subscrição ou o ID da saída do comando anterior:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Obter os detalhes de conta de armazenamento

Os seguintes passos partem do princípio de que criou a conta de armazenamento utilizando o **Resource Manager** modelo de implementação e não o **clássico** modelo de implementação.

Para configurar os carregamentos de ficheiros dos seus dispositivos, precisa de cadeia de ligação para uma conta de armazenamento do Azure. A conta de armazenamento tem de ser na mesma subscrição do seu IoT hub. Também tem o nome de um contentor de blob na conta de armazenamento. Utilize o seguinte comando para obter as chaves de conta de armazenamento:

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Anote o **chave1** valor de chave de conta de armazenamento. Terá nos passos seguintes.

Pode utilizar um contentor de blob existente para carregamentos de ficheiros ou crie uma nova:

* Para listar os contentores de blob existente na sua conta de armazenamento, utilize os seguintes comandos:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* Para criar um contentor de BLOBs na sua conta de armazenamento, utilize os seguintes comandos:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configurar o seu IoT hub

Agora pode configurar o seu IoT hub para ativar [funcionalidade de carregamento de ficheiros] [ lnk-upload] utilizando os detalhes de conta de armazenamento.

A configuração requer os seguintes valores:

**Contentor de armazenamento**: um contentor do blob numa conta de armazenamento do Azure na sua subscrição do Azure atual para associar o seu IoT hub. Obter as informações de conta de armazenamento necessário na secção anterior. IoT Hub gera automaticamente SAS URIs com permissões de escrita para este contentor de BLOBs para os dispositivos a utilizar ao carregam estes ficheiros.

**Receber notificações para os ficheiros carregados**: Ativar ou desativar notificações de carregamento de ficheiros.

**TTL de SAS**: esta definição é o tempo-to-live do URI de SAS devolvido para o dispositivo ao IoT Hub. Defina uma hora por predefinição.

**Notificação de definições predefinidas TTL ficheiros**: O time-to-live de um ficheiro a carregar notificação antes de expirar. Definido para um dia por predefinição.

**Contagem máxima de entrega de notificação de ficheiros**: O número de vezes que o IoT Hub tenta fornecer um ficheiro de notificação de carregar. Definido para 10 por predefinição.

Utilize o seguinte cmdlet do PowerShell para configurar o ficheiro de carregar as definições no seu IoT hub:

```powershell
Set-AzureRmIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as capacidades de carregamento do ficheiro do IoT Hub, consulte [carregar ficheiros a partir de um dispositivo][lnk-upload].

Siga estas ligações para saber mais sobre a gestão do Azure IoT Hub:

* [Em massa gerir dispositivos de IoT][lnk-bulk]
* [Métricas de IoT Hub][lnk-metrics]
* [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]
* [Proteger a sua solução de IoT a partir do zero cópias de segurança][lnk-securing]

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-powershell-storage]: https://docs.microsoft.com/powershell/module/azurerm.storage/
[lnk-powershell-iothub]: https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub
[lnk-portal-hub]: iot-hub-create-through-portal.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md