---
title: Criar um IoT Hub com a CLI do Azure (az.py) | Microsoft Docs
description: "Como criar um hub IoT do Azure utilizando o 2.0 de CLI do Azure de várias plataformas (az.py)."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 161089159999a4a63a39b059e69a08b7a9297445
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Criar um hub IoT utilizando o 2.0 CLI do Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar o Azure CLI 2.0 (az.py) para criar e gerir os hubs IoT do Azure através de programação. Este artigo mostra como utilizar o 2.0 do CLI do Azure (az.py) para criar um hub IoT.

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

* [CLI do Azure (azure.js)](iot-hub-create-using-cli-nodejs.md) – a CLI para os modelos de implementação clássica e resource Gestão.
* Azure CLI 2.0 (az.py) - a próxima geração CLI para o modelo de implementação de gestão recursos, tal como descrito neste artigo.

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [CLI do Azure 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>A iniciar sessão e definir a sua conta do Azure

Inicie sessão na sua conta do Azure e selecionar a sua subscrição.

1. Na linha de comandos, execute o [comando de início de sessão][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Siga as instruções para efetuar a autenticação com o código e inicie sessão na sua conta do Azure através de um browser.

2. Se tiver várias subscrições do Azure, o início de sessão Azure concede acesso a todas as contas do Azure associada com as suas credenciais. Utilize o seguinte [comando para listar as contas do Azure] [ lnk-az-account-command] disponíveis para que possa utilizar:
    
    ```azurecli
    az account list 
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu IoT hub. Pode utilizar o nome da subscrição ou o ID da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>Criar um Hub IoT

Utilize a CLI do Azure para criar um grupo de recursos e, em seguida, adicione um IoT hub.

1. Quando criar um hub IoT, tem de criá-la num grupo de recursos. Utilize um grupo de recursos existente ou execute o seguinte [comando para criar um grupo de recursos][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > O exemplo anterior cria o grupo de recursos na localização EUA oeste. Pode ver uma lista de localizações disponíveis, executando o comando `az account list-locations -o table`.
    >
    >

2. Execute o seguinte [comando para criar um hub IoT] [ lnk-az-iot-command] no seu grupo de recursos, utilizando um nome globalmente exclusivo para o seu IoT hub:
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> O comando anterior cria um IoT hub no S1 para o qual é-lhe faturado do escalão de preço. Para obter mais informações, consulte [preços do IoT Hub do Azure][lnk-iot-pricing].
>
>

## <a name="remove-an-iot-hub"></a>Remover um Hub IoT

Pode utilizar a CLI do Azure para [eliminar um recurso individual][lnk-az-resource-command], tais como um IoT hub ou eliminar um grupo de recursos e todos os respetivos recursos, incluindo todos os IoT hubs.

Para eliminar um hub IoT, execute o seguinte comando:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Para eliminar um grupo de recursos e todos os respetivos recursos, execute o seguinte comando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como desenvolver para o IoT Hub, consulte os artigos seguintes:

* [Guia para programadores do IoT Hub][lnk-devguide]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [No portal do Azure para gerir o IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
