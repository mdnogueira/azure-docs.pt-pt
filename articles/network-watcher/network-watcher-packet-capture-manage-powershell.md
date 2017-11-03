---
title: Gerir capturas de pacotes com observador de rede do Azure - PowerShell | Microsoft Docs
description: "Esta página explica como gerir a funcionalidade de captura de pacotes do observador de rede com o PowerShell"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 5c6012ffea84c0fb0ffff95978af0145b8d40422
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Gerir capturas de pacotes com observador de rede do Azure com o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

Captura de pacotes do observador de rede permite-lhe criar sessões de captura para controlar o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura garantir que apenas o tráfego que pretende capturar. Captura de pacotes ajuda-o para diagnosticar anomalias de rede de forma reativa e de forma pró-ativa. Outras utilizações incluem a recolha de estatísticas de rede, obtenha informações sobre intrusions de rede, para depurar as comunicações cliente-servidor e muito mais. Sendo possível para acionar remotamente capturas de pacotes, esta capacidade facilita o fardo de executar uma captura de pacotes manualmente e na máquina pretendida, que poupa tempo importante.

Este artigo orienta-as tarefas de gestão diferentes que estão atualmente disponíveis para captura de pacotes.

- [**Inicie uma captura de pacotes**](#start-a-packet-capture)
- [**Parar uma captura de pacotes**](#stop-a-packet-capture)
- [**Eliminar uma captura de pacotes**](#delete-a-packet-capture)
- [**Transferir uma captura de pacotes**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem os seguintes recursos:

* Uma instância do observador de rede na região onde pretende criar uma captura de pacotes

* Uma máquina virtual com a extensão de captura de pacotes ativada.

> [!IMPORTANT]
> Captura de pacotes requer uma extensão da máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão numa Windows VM visite [extensão da máquina virtual de agente de observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para, visite VM com Linux [extensão da máquina virtual de agente de observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalar a extensão de VM

### <a name="step-1"></a>Passo 1

```powershell
$VM = Get-AzureRmVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Passo 2

O exemplo seguinte obtém as informações de extensão necessárias para executar o `Set-AzureRmVMExtension` cmdlet. Este cmdlet instala o agente de captura de pacotes na máquina virtual convidada.

> [!NOTE]
> O `Set-AzureRmVMExtension` cmdlet poderá demorar vários minutos a concluir.

Para máquinas virtuais do Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Para máquinas virtuais do Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
````

O exemplo seguinte é uma resposta com êxito após a execução de `Set-AzureRmVMExtension` cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Passo 3

Para garantir que o agente está instalado, execute o `Get-AzureRmVMExtension` cmdlet e transmita-o nome da máquina virtual e o nome de extensão.

```powershell
Get-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

O exemplo seguinte é um exemplo de resposta a execução`Get-AzureRmVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Inicie uma captura de pacotes

Depois dos passos anteriores estiverem concluídos, o agente de captura de pacotes está instalado na máquina virtual.

### <a name="step-1"></a>Passo 1

O próximo passo é obter a instância de observador de rede. Esta variável é transmitida para o `New-AzureRmNetworkWatcherPacketCapture` cmdlet no passo 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName  
```

### <a name="step-2"></a>Passo 2

Obter uma conta de armazenamento. Esta conta de armazenamento é utilizada para armazenar o ficheiro de captura de pacotes.

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Passo 3

Podem ser utilizados filtros para limitar os dados que são armazenados pela captura de pacotes. O exemplo a seguir configura dois filtros.  Um filtro recolhe o tráfego de TCP de saída apenas a partir de local IP 10.0.0.3 às portas de destino, 20, 80 e 443.  O segundo filtro recolhe apenas o tráfego UDP.

```powershell
$filter1 = New-AzureRmPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzureRmPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Vários filtros podem ser definidos para uma captura de pacotes.

### <a name="step-4"></a>Passo 4

Execute o `New-AzureRmNetworkWatcherPacketCapture` cmdlet para iniciar o processo de captura de pacotes, transmitir os valores necessários obtidos nos passos anteriores.
```powershell

New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filters $filter1, $filter2
```

O exemplo seguinte é o resultado esperado da execução de `New-AzureRmNetworkWatcherPacketCapture` cmdlet.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Obtenha uma captura de pacotes

Executar o `Get-AzureRmNetworkWatcherPacketCapture` cmdlet, obtém o estado de uma captura de pacotes em execução, ou foi concluída.

```powershell
Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

O exemplo seguinte é o resultado do `Get-AzureRmNetworkWatcherPacketCapture` cmdlet. O exemplo seguinte é depois de concluída a captura. O valor de PacketCaptureStatus está parado com um StopReason TimeExceeded. Este valor mostra que a captura de pacotes foi concluída com êxito e de que executou a respetiva hora.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacotes

Ao executar o `Stop-AzureRmNetworkWatcherPacketCapture` cmdlet, se uma sessão de captura está em curso está parado.

```powershell
Stop-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> O cmdlet devolve sem resposta quando executou uma sessão de captura atualmente em execução ou de uma sessão existente que já foi parado.

## <a name="delete-a-packet-capture"></a>Eliminar uma captura de pacotes

```powershell
Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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

Determinar se determinados tráfego é permitido no orr fora da sua VM, visitando [Certifique-se de fluxo de verificação de IP](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->














