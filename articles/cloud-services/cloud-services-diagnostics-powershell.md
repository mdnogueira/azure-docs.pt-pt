---
title: "Ativar o diagnóstico nos serviços de nuvem do Azure com o PowerShell | Microsoft Docs"
description: "Saiba como ativar o diagnóstico para serviços em nuvem com o PowerShell"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo
ms.openlocfilehash: 8dd9724981860c9cd4ccc443cc2bfdc465811e7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Ativar o diagnóstico nos serviços de nuvem do Azure com o PowerShell
É possível recolher dados de diagnóstico, como os registos de aplicações, contadores de desempenho etc. a partir de um serviço em nuvem através da extensão de diagnóstico do Azure. Este artigo descreve como ativar a extensão de diagnóstico do Azure para um serviço em nuvem com o PowerShell.  Consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para os pré-requisitos necessários para este artigo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Ativar a extensão de diagnóstico como parte da implementação de um Serviço Cloud
Esta abordagem é aplicável ao tipo de integração contínua dos cenários, onde a extensão de diagnóstico pode ser ativada como parte da implementação do serviço de nuvem. Ao criar uma nova implementação de serviço em nuvem pode ativar a extensão de diagnóstico mediante a transmissão no *ExtensionConfiguration* parâmetro para o [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-3.7.0) cmdlet. O *ExtensionConfiguration* parâmetro assume uma matriz de configurações de diagnóstico que podem ser criadas utilizando o [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) cmdlet.

O exemplo seguinte mostra como pode ativar o diagnóstico num serviço em nuvem com um WebRole e WorkerRole, cada um com uma configuração de diagnósticos diferentes.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Se o ficheiro de configuração de diagnósticos Especifica um `StorageAccount` elemento com um nome de conta de armazenamento, em seguida, a `New-AzureServiceDiagnosticsExtensionConfig` cmdlet automaticamente irá utilizar essa conta de armazenamento. Para este funcione, a conta de armazenamento tem de estar na mesma subscrição do serviço em nuvem que está a ser implementada.

Azure SDK versão 2.6 onward publicar os ficheiros de configuração de extensão gerados do MSBuild a saída do destino incluirá o nome da conta de armazenamento com base na cadeia de configuração de diagnósticos especificada no ficheiro de configuração do serviço (. cscfg). O script abaixo mostra como analisar os ficheiros de configuração de extensão da saída do destino de publicar e configurar a extensão de diagnóstico para cada função quando implementar o serviço em nuvem.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online utiliza uma abordagem semelhante para implementações automatizadas dos serviços em nuvem com a extensão de diagnóstico. Consulte [publicar AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) para obter um exemplo completo.

Se não `StorageAccount` foi especificado na configuração de diagnósticos, em seguida, tem de passar o *StorageAccountName* parâmetro para o cmdlet. Se o *StorageAccountName* parâmetro for especificado, em seguida, o cmdlet irá utilizar sempre a conta de armazenamento que é especificada o parâmetro e não aquele que está especificado no ficheiro de configuração de diagnóstico.

Se a conta de armazenamento do diagnostics está numa subscrição diferente do serviço de nuvem, em seguida, tem de passar explicitamente o *StorageAccountName* e *StorageAccountKey* parâmetros para o cmdlet. O *StorageAccountKey* parâmetro não é necessária quando a conta de armazenamento do diagnostics está na mesma subscrição, como o cmdlet automaticamente pode consultar e defina o valor de chave ao ativar a extensão de diagnóstico. No entanto, se a conta de armazenamento do diagnostics está numa subscrição diferente, em seguida, o cmdlet poderá não conseguir obter automaticamente a chave e tem de explicitamente especificar a chave através de *StorageAccountKey* parâmetro.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Ativar a extensão de diagnóstico num Serviço Cloud existente
Pode utilizar o [conjunto AzureServiceDiagnosticsExtension](/powershell/module/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet para ativar ou Atualize a configuração de diagnóstico num serviço em nuvem que já está em execução.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração atual da extensão de diagnóstico
Utilize o [Get-AzureServiceDiagnosticsExtension](/powershell/module/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet para obter a configuração de diagnósticos atual para um serviço em nuvem.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Remover a extensão de diagnóstico
Para desativar o diagnóstico num serviço em nuvem que pode utilizar o [remover AzureServiceDiagnosticsExtension](/powershell/module/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se tiver ativado a extensão de diagnóstico com *conjunto AzureServiceDiagnosticsExtension* ou o *New-AzureServiceDiagnosticsExtensionConfig* sem o *função* parâmetro, em seguida, que pode remover a extensão com *remover AzureServiceDiagnosticsExtension* sem o *função* parâmetro. Se o *função* parâmetro foi utilizado ao ativar a extensão, em seguida, este tem também de ser utilizada para remover a extensão.

Para remover a extensão de diagnóstico de cada função individual:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter orientações adicionais sobre como utilizar outras técnicas e diagnóstico do Azure para resolver problemas, consulte [ativar diagnósticos no Cloud Services do Azure e máquinas virtuais](cloud-services-dotnet-diagnostics.md).
* O [esquema de configuração de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) explica as várias opções de configurações de xml para a extensão de diagnóstico.
* Para saber como ativar a extensão de diagnóstico para máquinas virtuais, consulte [criar uma Máquina Virtual do Windows com a monitorização e diagnóstico com o modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md)
