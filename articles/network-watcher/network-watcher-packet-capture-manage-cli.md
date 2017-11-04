---
title: Gerir capturas de pacotes com observador de rede do Azure - Azure CLI 2.0 | Microsoft Docs
description: "Esta página explica como gerir a funcionalidade de captura de pacotes do observador de rede a utilizar o Azure CLI 2.0"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 66f53f97220f8fd23fa38bece0025f8b48289e23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-20"></a>Gerir capturas de pacotes com observador de rede do Azure a utilizar o Azure CLI 2.0

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

Captura de pacotes do observador de rede permite-lhe criar sessões de captura para controlar o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura garantir que apenas o tráfego que pretende capturar. Captura de pacotes ajuda-o para diagnosticar anomalias de rede de forma reativa e de forma pró-ativa. Outras utilizações incluem a recolha de estatísticas de rede, obtenha informações sobre intrusions de rede, para depurar as comunicações cliente-servidor e muito mais. Sendo possível para acionar remotamente capturas de pacotes, esta capacidade facilita o fardo de executar uma captura de pacotes manualmente e na máquina pretendida, que poupa tempo importante.

Este artigo utiliza o nosso CLI de próxima geração para o modelo de implementação de gestão de recursos, Azure CLI 2.0, que está disponível para o Windows, Mac e Linux.

Para efetuar os passos neste artigo, terá de [instalar a Interface de linha de comandos do Azure para Mac, Linux e Windows (CLI do Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

Este artigo orienta-as tarefas de gestão diferentes que estão atualmente disponíveis para captura de pacotes.

- [**Inicie uma captura de pacotes**](#start-a-packet-capture)
- [**Parar uma captura de pacotes**](#stop-a-packet-capture)
- [**Eliminar uma captura de pacotes**](#delete-a-packet-capture)
- [**Transferir uma captura de pacotes**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem os seguintes recursos:

- Uma instância do observador de rede na região onde pretende criar uma captura de pacotes
- Uma máquina virtual com a extensão de captura de pacotes ativada.

> [!IMPORTANT]
> Captura de pacotes necessita de um agente está em execução na máquina virtual. O agente está instalado como uma extensão. Para obter instruções sobre como extensões VM, visite [extensões de Máquina Virtual e funcionalidades](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Instalar a extensão de VM

### <a name="step-1"></a>Passo 1

Execute o `az vm extension set` cmdlet para instalar o agente de captura de pacotes na máquina virtual convidada.

Para máquinas virtuais do Windows:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Para máquinas virtuais do Linux:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
````

### <a name="step-2"></a>Passo 2

Para garantir que o agente está instalado, execute o `vm extension show` cmdlet e transmita-o nome de grupo e a máquina virtual de recursos. Verifique a lista resultante para garantir que o agente está instalado.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

O exemplo seguinte é um exemplo de resposta a execução`az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Inicie uma captura de pacotes

Depois dos passos anteriores estiverem concluídos, o agente de captura de pacotes está instalado na máquina virtual.

### <a name="step-1"></a>Passo 1

O próximo passo é obter a instância de observador de rede. Nome de tNão do observador de rede é transmitido para o `az network watcher show` cmdlet no passo 4.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>Passo 2

Obter uma conta de armazenamento. Esta conta de armazenamento é utilizada para armazenar o ficheiro de captura de pacotes.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Passo 3

Podem ser utilizados filtros para limitar os dados que são armazenados pela captura de pacotes. O exemplo a seguir configura uma captura de pacotes com vários filtros.  Os três primeiros filtros recolher o tráfego de TCP de saída apenas a partir de local IP 10.0.0.3 às portas de destino, 20, 80 e 443.  O filtro de último recolhe apenas o tráfego UDP.

```azurecli
az network watcher packet-capture create --resource-group {resoureceurceGroupName} --vm {vmName} --name packetCaptureName --storage-account gwteststorage123abc --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

O exemplo seguinte é o resultado esperado da execução de `az network watcher packet-capture create` cmdlet.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Obtenha uma captura de pacotes

Executar o `az network watcher packet-capture show` cmdlet, obtém o estado de uma captura de pacotes em execução, ou foi concluída.

```azurecli
az network watcher packet-capture show --name packetCaptureName --location westcentralus
```

O exemplo seguinte é o resultado do `az network watcher packet-capture show` cmdlet. O exemplo seguinte é depois de concluída a captura. O valor de PacketCaptureStatus está parado com um StopReason TimeExceeded. Este valor mostra que a captura de pacotes foi concluída com êxito e de que executou a respetiva hora.

```
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/providers/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapt
ure_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacotes

Ao executar o `az network watcher packet-capture stop` cmdlet, se uma sessão de captura está em curso está parado.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> O cmdlet devolve sem resposta quando executou uma sessão de captura atualmente em execução ou de uma sessão existente que já foi parado.

## <a name="delete-a-packet-capture"></a>Eliminar uma captura de pacotes

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Eliminar uma captura de pacotes não elimina o ficheiro na conta de armazenamento.

## <a name="download-a-packet-capture"></a>Transferir uma captura de pacotes

Depois de concluída a sua sessão de captura de pacote, o ficheiro de captura pode ser carregado para o blob storage ou para um ficheiro local na VM. A localização de armazenamento da captura de pacotes está definida na criação da sessão. Uma ferramenta conveniente para aceder a estes ficheiros de captura guardados para uma conta de armazenamento é Explorador de armazenamento do Microsoft Azure, que pode ser transferida aqui: http://storageexplorer.com/

Se for especificada uma conta de armazenamento, ficheiros de captura de pacotes são guardados para uma conta de armazenamento disponíveis na seguinte localização:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Passos seguintes

Saiba como automatizar capturas de pacotes com alertas de Máquina Virtual visualizando [criar uma captura de pacotes accionadas alerta](network-watcher-alert-triggered-packet-capture.md)

Determinar se determinados o tráfego é permitido dentro ou fora da sua VM, visitando [Certifique-se de fluxo de verificação de IP](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
