---
title: Configurar o carregamento de ficheiros ao IoT Hub com a CLI do Azure (az.py) | Microsoft Docs
description: "Como configurar fileuploads ao Azure IoT Hub com a 2.0 de CLI do Azure de várias plataformas (az.py)."
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
ms.openlocfilehash: 6b100e65aba604fd8becb02c3a205b3348872bc4
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configurar o IoT Hub carregamentos de ficheiros utilizando a CLI do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para utilizar o [funcionalidade de carregamento de ficheiros no IoT Hub][lnk-upload], tem primeiro de associar uma conta de armazenamento do Azure com o seu IoT hub. Pode utilizar uma conta de armazenamento existente ou crie um novo.

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [CLI do Azure 2.0][lnk-CLI-install].
* Um hub IoT do Azure. Se não tiver um IoT hub, pode utilizar o `az iot hub create` [comando] [ lnk-cli-create-iothub] para criar um ou utilize o portal para [criar um hub IoT] [Ink-portal-hub].
* Uma conta de armazenamento do Azure. Se não tiver uma conta de armazenamento do Azure, pode utilizar o [2.0 do CLI do Azure - gerir contas de armazenamento] [ lnk-manage-storage] para criar um ou utilizar o portal para [criar uma conta de armazenamento] [ lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>A iniciar sessão e definir a sua conta do Azure

Inicie sessão na sua conta do Azure e selecionar a sua subscrição.

1. Na linha de comandos, execute o [comando de início de sessão][lnk-login-command]:

    ```azurecli
    az login
    ```

    Siga as instruções para efetuar a autenticação com o código e inicie sessão na sua conta do Azure através de um browser.

1. Se tiver várias subscrições do Azure, o início de sessão Azure concede acesso a todas as contas do Azure associada com as suas credenciais. Utilize o seguinte [comando para listar as contas do Azure] [ lnk-az-account-command] disponíveis para que possa utilizar:

    ```azurecli
    az account list
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu IoT hub. Pode utilizar o nome da subscrição ou o ID da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Obter os detalhes de conta de armazenamento

Os seguintes passos partem do princípio de que criou a conta de armazenamento utilizando o **Resource Manager** modelo de implementação e não o **clássico** modelo de implementação.

Para configurar os carregamentos de ficheiros dos seus dispositivos, precisa de cadeia de ligação para uma conta de armazenamento do Azure. A conta de armazenamento tem de ser na mesma subscrição do seu IoT hub. Também tem o nome de um contentor de blob na conta de armazenamento. Utilize o seguinte comando para obter as chaves de conta de armazenamento:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Anote o **connectionString** valor. Terá nos passos seguintes.

Pode utilizar um contentor de blob existente para carregamentos de ficheiros ou crie uma nova:

* Para listar os contentores de blob existente na sua conta de armazenamento, utilize o seguinte comando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Para criar um contentor de BLOBs na sua conta de armazenamento, utilize o seguinte comando:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Carregamento de ficheiros

Agora pode configurar o seu IoT hub para ativar [funcionalidade de carregamento de ficheiros] [ lnk-upload] utilizando os detalhes de conta de armazenamento.

A configuração requer os seguintes valores:

**Contentor de armazenamento**: um contentor do blob numa conta de armazenamento do Azure na sua subscrição do Azure atual para associar o seu IoT hub. Obter as informações de conta de armazenamento necessário na secção anterior. IoT Hub gera automaticamente SAS URIs com permissões de escrita para este contentor de BLOBs para os dispositivos a utilizar ao carregam estes ficheiros.

**Receber notificações para os ficheiros carregados**: Ativar ou desativar notificações de carregamento de ficheiros.

**TTL de SAS**: esta definição é o tempo-to-live do URI de SAS devolvido para o dispositivo ao IoT Hub. Defina uma hora por predefinição.

**Notificação de definições predefinidas TTL ficheiros**: O time-to-live de um ficheiro a carregar notificação antes de expirar. Definido para um dia por predefinição.

**Contagem máxima de entrega de notificação de ficheiros**: O número de vezes que o IoT Hub tenta fornecer um ficheiro de notificação de carregar. Definido para 10 por predefinição.

Utilize os seguintes comandos da CLI do Azure para configurar as definições de carregamento de ficheiros no seu IoT hub:

Uma utilização da shell de bash:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Cada uma utilização de linha de comandos do Windows:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Pode rever a configuração de carregamento de ficheiros no seu IoT hub, utilizando o seguinte comando:

```azurecli
az iot hub show --name {your iot hub name}
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

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]:../storage/common/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az_iot_hub_create