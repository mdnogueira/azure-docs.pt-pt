---
title: Utilizar scripts do Azure Resource Manager para gerir dispositivos StorSimple | Microsoft Docs
description: Saiba como utilizar o Azure Resource Manager scripts para automatizar as tarefas do StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 4bb6becd0b664b9287a1973d5221cff46dca57da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Utilizar scripts baseados no SDK do Azure Resource Manager para gerir dispositivos StorSimple

Este artigo descreve como SDK do Gestor de recursos do Azure com base em scripts podem ser utilizadas para gerir o seu dispositivo de série 8000 do StorSimple. Um script de exemplo também está incluído para guiá-lo através dos passos de configuração do seu ambiente para executar estes scripts.

Este artigo aplica-se em execução no portal do Azure apenas em dispositivos série 8000 do StorSimple.

## <a name="sample-scripts"></a>Scripts de exemplo

Os scripts de exemplo seguintes estão disponíveis para automatizar várias tarefas do StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabela de scripts de exemplo com base no SDK do Azure Resource Manager

| Script de Gestor de recursos do Azure                    | Descrição                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [ServiceEncryptionRollover.ps1 autorizar](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Este script permite-lhe autorizar o dispositivo StorSimple para alterar a chave de encriptação de dados do serviço.                                                                                                           |
| [StorSimpleCloudAppliance.ps1 criar](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Este script cria um dispositivo de nuvem do StorSimple 8010 ou de uma 8020. A aplicação de nuvem, em seguida, pode ser configurada e registada no serviço StorSimple Manager de dados.                                                       |
| [CreateOrUpdate Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Este script cria ou modifica os volumes do StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Este script apresenta uma lista de todas as cópias de segurança para um dispositivo registado com o serviço do Gestor de dispositivos do StorSimple.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Este script todas as políticas de cópia de segurança para o dispositivo StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Este script obtém todas as tarefas do StorSimple em execução no serviço StorSimple Manager do dispositivo.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Este script analisa o servidor de atualização e permite-lhe saber se as atualizações estiverem disponíveis para instalação no dispositivo StorSimple.                                                                                          |
| [Instalação DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Este script instala as atualizações disponíveis no dispositivo StorSimple.                                                                                                                                           |
| [CloudSnapshots.ps1 gerir](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Este script inicia um instantâneo de nuvem manual e elimina os instantâneos de nuvem com mais de dias de retenção especificado.                                                                                                   |
| [Monitor Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Este script do PowerShell do Runbook de automatização do Azure reporta o estado de todas as tarefas de cópia de segurança.                                                                                                              |
| [Remover DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Este script elimina um único objeto de cópia de segurança.                                                                                                                                                           |
| [Início DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Este script inicia uma cópia de segurança manual no dispositivo StorSimple.                                                                                                                                       |
| [Atualização CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Este script atualizações a chave de encriptação de dados do serviço para todos os dispositivos de nuvem do 8010/8020 StorSimple registados com o serviço do Gestor de dispositivos do StorSimple.                                     |
| [Certifique-se-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Este script realça as cópias de segurança em falta depois de analisar todos os agendamentos associados a políticas de cópia de segurança. Também verifica o catálogo de cópias de segurança com a lista de cópias de segurança disponíveis.             |




## <a name="configure-and-run-a-sample-script"></a>Configurar e executar um script de exemplo

Esta secção assume um script de exemplo e fornece detalhes sobre os vários passos necessários para executar o script.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

*   O Azure PowerShell instalado. Para instalar os módulos do Azure PowerShell:
    * Num ambiente Windows, siga os passos no [instalar e configurar o Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0). Pode instalar o Azure PowerShell no anfitrião do Windows Server para o StorSimple se utilizar um.
    * Num ambiente de Linux ou MacOS, siga os passos no [instalar e configurar o Azure PowerShell no MacOS ou Linux](https://docs.microsoft.com/en-us/powershell/azure/install-azurermps-maclinux?view=azurermps-4.4.0).

Para obter mais informações sobre como utilizar o Azure PowerShell, aceda a [começar a utilizar o Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps?view=azurermps-4.4.0).

### <a name="run-azure-powershell-script"></a>Executar script do PowerShell do Azure

O script utilizado neste exemplo apresenta uma lista de todas as tarefas num dispositivo StorSimple. Isto inclui as tarefas que foi concluída com êxito, falharam ou estão em curso. Execute os seguintes passos para transferir e executar o script.

1. Inicie o Azure PowerShell. Crie uma nova pasta e altere o diretório para a nova pasta.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Transferir a CLI do NuGet](http://www.nuget.org/downloads) sob a pasta que criou no passo anterior. Existem várias versões do _nuget.exe_. Escolha a versão correspondente para o SDK. Cada ligação de transferência aponta diretamente para um _.exe_ ficheiro. Certifique-se com o botão direito e guarde o ficheiro para o seu computador em vez de executar a partir do browser.

    Também pode executar o seguinte comando para transferir e armazenar o script na mesma pasta que criou anteriormente.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Transferir o SDK dependente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Transferir o script do projeto de GitHub de exemplo.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Execute o script. Quando solicitado para autenticar, forneça as credenciais do Azure. Este script deve saída uma lista filtrada de todas as tarefas no dispositivo StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Resultado da amostra

O seguinte resultado é apresentado quando o script de exemplo é executado. A saída contém todas as tarefas que tenha sido executada num dispositivo registado que foi iniciado em 25 de Setembro de 2017 e concluída até 2 de Outubro de 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Passos seguintes

[Serviço do Gestor de dispositivos do StorSimple de utilização para gerir o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).