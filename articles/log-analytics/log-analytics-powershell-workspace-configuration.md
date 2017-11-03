---
title: "Utilizar o PowerShell para criar e configurar uma área de trabalho de análise do registo | Microsoft Docs"
description: "Iniciar análise utiliza dados a partir de servidores no local ou a infraestrutura de nuvem. Pode recolher dados da máquina de armazenamento do Azure quando gerados ao diagnóstico do Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 6807ab67e3593da82c147669b29bfdae3b6c967c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-log-analytics-using-powershell"></a>Gerir o Log Analytics com o PowerShell
Pode utilizar o [cmdlets do PowerShell de análise do registo](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para efetuar várias funções na análise de registos, numa linha de comandos ou como parte de um script.  Exemplos de tarefas que pode realizar com o PowerShell incluem:

* Criar áreas de trabalho
* Adicionar ou remover uma solução
* Importar e exportar procuras guardadas
* Criar um grupo de computadores
* Ativar a recolha de registos de IIS a partir de computadores com o agente de Windows instalado
* Recolher contadores de desempenho de computadores com Linux e Windows
* Recolher eventos do syslog nos computadores com Linux 
* Recolher eventos de registos de eventos do Windows
* Recolher registos de eventos personalizados
* Adicionar o agente de análise do registo para uma máquina virtual do Azure
* Configurar a análise de registos para dados do índice recolhidos através de diagnóstico do Azure

Este artigo fornece dois exemplos de código que ilustram algumas das funções que pode realizar a partir do PowerShell.  Pode consultar o [referência de cmdlets PowerShell de análise do registo](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para outras funções.

> [!NOTE]
> Análise de registos foi anteriormente denominada das informações operacionais, que é o nome utilizado nos cmdlets.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Estes exemplos funcionam com a versão 2.3.0 ou posterior do módulo AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Criar e configurar uma área de trabalho de análise do registo
O script de exemplo seguinte ilustra como:

1. Criar áreas de trabalho
2. Lista as soluções disponíveis
3. Adicionar soluções para a área de trabalho
4. Pesquisas de importação guardada
5. Exportação guardada pesquisas
6. Criar um grupo de computadores
7. Ativar a recolha de registos de IIS a partir de computadores com o agente de Windows instalado
8. Recolher contadores de desempenho disco lógico a partir de computadores com Linux (% de Inodes utilizados; Megabytes livres; % De espaço; utilizado Transferências/seg do disco; Leituras de disco/seg; Escritas de disco/seg)
9. Recolher eventos syslog de computadores com Linux
10. Recolher eventos de erro e aviso do registo de eventos de computadores Windows
11. Recolher contador de desempenho Memória \ Mbytes disponíveis a partir de computadores Windows
12. Recolher um registo personalizado 

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configurar a análise de registos para indexar o diagnóstico do Azure
Para a monitorização sem agente de recursos do Azure, os recursos tem de ter o diagnóstico do Azure ativada e configurada para escrever uma área de trabalho de análise de registos. Esta abordagem envia dados diretamente para a análise de registos e não necessita de dados para ser escrito para uma conta de armazenamento. Recursos suportados incluem:

| Tipo de Recurso | Registos | Métricas |
| --- | --- | --- |
| Gateways da Aplicação    | Sim | Sim |
| Contas de Automatização     | Sim | |
| Contas do batch          | Sim | Sim |
| Análise do Data Lake     | Sim | | 
| Arquivo data Lake         | Sim | |
| Agrupamento elástico de SQL        |     | Sim |
| Espaço de nomes de Hub de eventos     |     | Sim |
| Hubs IoT                |     | Sim |
| Cofre de Chaves               | Sim | |
| Balanceadores de carga          | Sim | |
| Aplicações Lógicas              | Sim | Sim |
| Grupos de Segurança de Rede | Sim | |
| Cache de Redis             |     | Sim |
| Procurar serviços         | Sim | Sim |
| Espaço de nomes do Service Bus   |     | Sim |
| SQL (v12)               |     | Sim |
| Web Sites               |     | Sim |
| Web farms de servidores        |     | Sim |

Para obter os detalhes sobre as métricas disponíveis, consulte [suportado métricas com a monitorização do Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Para obter os detalhes dos registos disponíveis, consulte [suportado serviços e o esquema para os registos de diagnóstico](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Também pode utilizar o cmdlet anterior para recolher registos de recursos que estão em subscrições diferentes. O cmdlet é capaz de funcionar entre subscrições, uma vez que está a fornecer o id do recurso a criar os registos e os registos são enviados para a área de trabalho.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Configurar a análise de registos para diagnósticos do Azure do armazenamento de índice
Para recolher dados de registo a partir de dentro de uma instância em execução de um serviço em nuvem clássico ou um cluster do service fabric, terá de escrever primeiro os dados para o armazenamento do Azure. Análise de registos é então configurada para recolher os registos da conta do storage. Recursos suportados incluem:

* Serviços de cloud clássico (funções web e de trabalho)
* Clusters de recursos de infraestrutura de serviço

O seguinte exemplo mostra como:

1. Lista de contas do storage existentes e localizações de análise de registos serão índice dados a partir de
2. Criar uma configuração de ler a partir de uma conta de armazenamento
3. Atualizar a configuração recentemente criada para dados do índice a partir de localizações adicionais
4. Eliminar a configuração criada recentemente

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

Também pode utilizar o script anterior para recolher registos de contas de armazenamento em diferentes subscrições. O script é capaz de funcionar entre subscrições, uma vez que está a fornecer o id de recurso de conta de armazenamento e uma chave de acesso correspondente. Quando alterar a chave de acesso, terá de atualizar as informações de armazenamento com a nova chave.


## <a name="next-steps"></a>Passos seguintes
* [Reveja os cmdlets do PowerShell de análise do registo](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para obter informações adicionais sobre como utilizar o PowerShell para análise de registos de configuração.

