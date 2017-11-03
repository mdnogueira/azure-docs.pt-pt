---
title: Utilizar o blob storage para o armazenamento IIS e a tabela de eventos no Log Analytics do Azure | Microsoft Docs
description: "Análise de registos pode ler os registos de serviços do Azure que escrever diagnósticos para armazenamento de tabelas ou escritos para o armazenamento de BLOBs de registos de IIS."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 459ef90ca1d76bada6565bfefd7b4bd1086197d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Utilizar o armazenamento de Blobs do Azure para armazenamento de tabelas IIS e o Azure para eventos com a análise de registos

Análise de registos pode ler os registos para os seguintes serviços escrever diagnósticos para armazenamento de tabelas ou os registos IIS escritos para armazenamento de BLOBs:

* Clusters do Service Fabric (pré-visualização)
* Virtual Machines
* Funções da Web/trabalho

Antes de análise de registos pode recolher dados para estes recursos, o diagnóstico do Azure tem de estar ativado.

Depois de diagnóstico está ativado, pode utilizar o portal do Azure ou análise de registos para recolher os registos de configurar o PowerShell.

Diagnóstico do Azure é uma extensão do Azure que permite-lhe recolher dados de diagnóstico de uma máquina virtual em execução no Azure, a função da web ou a função de trabalho. Os dados são armazenados numa conta de armazenamento do Azure e, em seguida, podem ser recolhidos através da análise de registos.

Para análise de registos recolher estes registos de diagnóstico do Azure, os registos tem de ser nas seguintes localizações:

| Tipo de registo | Tipo de Recurso | Localização |
| --- | --- | --- |
| Registos do IIS |Virtual Machines <br> Funções da Web <br> Funções de trabalho |WAD-iis-logfiles (armazenamento de BLOBs) |
| Syslog |Virtual Machines |LinuxsyslogVer2v0 (tabela de armazenamento) |
| Eventos operacionais de recursos de infraestrutura de serviço |Nós de Service Fabric |WADServiceFabricSystemEventTable |
| Eventos de Ator fiável de recursos de infraestrutura de serviço |Nós de Service Fabric |WADServiceFabricReliableActorEventTable |
| Eventos do serviço fiável de recursos de infraestrutura de serviço |Nós de Service Fabric |WADServiceFabricReliableServiceEventTable |
| Registos de eventos do Windows |Nós de Service Fabric <br> Virtual Machines <br> Funções da Web <br> Funções de trabalho |WADWindowsEventLogsTable (armazenamento de tabelas) |
| Registos de ETW do Windows |Nós de Service Fabric <br> Virtual Machines <br> Funções da Web <br> Funções de trabalho |WADETWEventTable (armazenamento de tabelas) |

> [!NOTE]
> Registos de IIS a partir de Web sites do Azure não são atualmente suportados.
>
>

Para máquinas virtuais, tem a opção de instalar o [agente da análise de registos](log-analytics-azure-vm-extension.md) na sua máquina virtual para ativar o insights adicionais. Além de poder analisar registos de eventos e registos de IIS, pode efetuar análise adicional, incluindo o controlo de alterações de configuração, avaliação do SQL Server e avaliação de atualização.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Ativar o diagnóstico do Azure numa máquina virtual para o registo de eventos e IIS de recolha de registos
Utilize o procedimento seguinte para ativar o diagnóstico do Azure numa máquina virtual para a recolha de registos de registo de eventos e IIS através do portal do Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Para ativar o diagnóstico do Azure numa máquina virtual com o portal do Azure
1. Instale o agente da VM ao criar uma máquina virtual. Se a máquina virtual já existir, certifique-se de que o agente da VM já está instalado.

   * No portal do Azure, navegue para a máquina virtual, selecione **configuração opcional**, em seguida, **diagnóstico** e defina **estado** para **no** .

     Após a conclusão, a VM tenha a extensão de diagnóstico do Azure instalados e em execução. Esta extensão é responsável pela recolha os dados de diagnóstico.
2. Ativar a monitorização e configurar uma VM existente registo de eventos. Pode ativar o diagnóstico ao nível da VM. Para ativar o diagnóstico e, em seguida, configurar o registo de eventos, execute os seguintes passos:

   1. Selecione a VM.
   2. Clique em **monitorização**.
   3. Clique em **diagnóstico**.
   4. Definir o **estado** para **ON**.
   5. Selecione cada registo de diagnóstico que pretende recolher.
   6. Clique em **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Ativar o diagnóstico do Azure numa função da Web para a coleção de registos e eventos IIS
Consulte [como para ativar o diagnóstico num serviço em nuvem](../cloud-services/cloud-services-dotnet-diagnostics.md) para passos gerais sobre como ativar o diagnóstico do Azure. As instruções abaixo utilizam estas informações e personalizá-lo para utilização com a análise de registos.

Com o diagnóstico do Azure ativada:

* Registos de IIS são armazenados por predefinição, com dados de registo transferidos num intervalo de transferência scheduledTransferPeriod.
* Registos de eventos do Windows não são transferidos por predefinição.

### <a name="to-enable-diagnostics"></a>Para ativar o diagnóstico
Para ativar registos de eventos do Windows ou para alterar o scheduledTransferPeriod, configure o diagnóstico do Azure utilizando o ficheiro de configuração XML (diagnostics.wadcfg), como mostrado na [passo 4: criar o ficheiro de configuração de diagnósticos e instalar a extensão](../cloud-services/cloud-services-dotnet-diagnostics.md)

O ficheiro de configuração de exemplo seguintes recolhe registos de IIS e todos os eventos dos registos de aplicações e do sistema:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Certifique-se de que o seu ConfigurationSettings Especifica uma conta de armazenamento, como no exemplo seguinte:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

O **AccountName** e **AccountKey** valores encontram-se no portal do Azure no dashboard de conta de armazenamento, em Gerir chaves de acesso. O protocolo para a cadeia de ligação tem de ser **https**.

Depois da configuração de diagnóstico atualizada é aplicada ao seu serviço de nuvem e de está a escrever diagnostics ao Storage do Azure, em seguida, estará pronto configurar a análise de registos.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Utilizar o portal do Azure para recolher registos de armazenamento do Azure
Pode utilizar o portal do Azure para configurar a análise de registos para recolher os registos para os seguintes serviços do Azure:

* Clusters de Service Fabric
* Virtual Machines
* Funções da Web/trabalho

No portal do Azure, navegue até à sua área de trabalho de análise de registos e efetuar as seguintes tarefas:

1. Clique em *registos de contas de armazenamento*
2. Clique em de *adicionar* tarefas
3. Selecione a conta de armazenamento que contém os registos de diagnóstico
   * Esta conta pode ser uma conta de armazenamento clássico ou uma conta do storage do Azure Resource Manager
4. Selecione o tipo de dados que pretende recolher registos
   * As opções são registos de IIS Eventos; Syslog (Linux); Registos ETW; Eventos de recursos de infraestrutura de serviço
5. O valor para a origem é preenchido automaticamente com base no tipo de dados e não pode ser alterado
6. Clique em OK para guardar a configuração

Repita os passos 2 a 6 para tipos de dados que pretende que a análise de registos para recolher e contas de armazenamento adicional.

Em aproximadamente 30 minutos, é capaz de ver dados da conta do storage na análise de registos. Vê apenas os dados que são escritos para armazenamento após a configuração é aplicada. Análise de registos não ler os dados previamente existentes da conta do storage.

> [!NOTE]
> O portal não valida se a origem existe na conta de armazenamento ou se estão a ser escritos novos dados.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Ativar o diagnóstico do Azure numa máquina virtual para utilizar o PowerShell de recolha de registos de registo de eventos e IIS
Utilize os passos em [Configurar análise de registos para diagnósticos do Azure de índice](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) para utilizar o PowerShell para ler a partir do diagnóstico do Azure que é escrito para armazenamento de tabelas.

Com o Azure PowerShell mais precisamente pode especificar os eventos que são escritos no armazenamento do Azure.
Para obter mais informações, consulte [ativar o diagnóstico em Azure Virtual Machines](../virtual-machines-dotnet-diagnostics.md).

Pode ativar e atualizar o diagnóstico do Azure utilizando o seguinte script do PowerShell.
Também pode utilizar este script com uma configuração de registo personalizado.
Modificar o script para configurar a conta de armazenamento, o nome do serviço e o nome da máquina virtual.
O script utiliza cmdlets para as máquinas virtuais clássicas.

Reveja o seguinte script de exemplo, copie-o, modifique-o conforme necessário, guardar o exemplo como um ficheiro de script do PowerShell e, em seguida, execute o script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Passos seguintes
* [Recolher os registos e as métricas para serviços do Azure](log-analytics-azure-storage.md) suportam serviços do Azure.
* [Permitir soluções](log-analytics-add-solutions.md) para obter informações sobre os dados.
* [Utilizar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.
