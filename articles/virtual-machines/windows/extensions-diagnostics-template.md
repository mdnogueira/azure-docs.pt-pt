---
title: "Adicionar monitorização e diagnóstico para uma máquina virtual do Azure | Microsoft Docs"
description: "Utilize um modelo de Gestor de recursos do Azure para criar uma nova máquina virtual do Windows com a extensão de diagnóstico do Azure."
services: virtual-machines-windows
documentationcenter: 
author: sbtron
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6955e3d8c7b032ee898be11e611080905b5069ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Utilizar a monitorização e diagnóstico com modelos de VM do Windows e o Azure Resource Manager
A extensão de diagnóstico do Azure fornece a monitorização e diagnóstico as capacidades do Windows baseado em máquina virtual do Azure. Pode ativar estas capacidades na máquina virtual, incluindo a extensão como parte do modelo de Gestor de recursos do azure. Consulte [criação de modelos de Gestor de recursos do Azure com extensões de VM](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) para obter mais informações sobre incluindo qualquer extensão como parte de um modelo de máquina virtual. Este artigo descreve como adicionar a extensão de diagnóstico do Azure para um modelo de máquina virtual do windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adicionar a extensão de diagnóstico do Azure para a definição do recurso VM
Para ativar a extensão de diagnóstico na máquina Virtual do Windows tem de adicionar a extensão como um recurso VM no modelo do Resource manager.

Para um Gestor de recursos simples baseada em Máquina Virtual da configuração de extensão para adicionar o *recursos* matriz para a Máquina Virtual: 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Outra convenção comum é a adicionar a configuração da extensão no nó raiz de recursos do modelo em vez de define sob o nó de recursos da máquina virtual. Com esta abordagem tem de especificar uma relação hierárquica entre a extensão e a máquina virtual com explicitamente o *nome* e *tipo* valores. Por exemplo: 

    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

A extensão é sempre associada com a máquina virtual, pode ser diretamente defini-lo diretamente no nó de recursos da máquina virtual ou defini-lo ao nível da base e utilizar a Convenção de nomenclatura hierárquica para o associar a máquina virtual.

Para conjuntos de dimensionamento de Máquina Virtual, a configuração de extensões é especificada no *extensionProfile* propriedade o *VirtualMachineProfile*.

O *publicador* propriedade com o valor de **Microsoft.Azure.Diagnostics** e *tipo* propriedade com o valor de **IaaSDiagnostics**identificam a extensão de diagnóstico do Azure.

O valor da *nome* propriedade pode ser utilizada para fazer referência para a extensão no grupo de recursos. Defini-la especificamente ao **Microsoft.Insights.VMDiagnosticsSettings** irá ativá-lo facilmente identificado pelo portal do Azure, garantindo que a monitorização de gráficos Mostrar segurança corretamente no portal do Azure.

O *typeHandlerVersion* Especifica a versão da extensão que pretende utilizar. Definição *autoUpgradeMinorVersion* versão secundária para **verdadeiro** garante que irá obter a versão secundária mais recente da extensão que está disponível. É altamente recomendável que defina sempre *autoUpgradeMinorVersion* sempre ser **verdadeiro** para que recebem sempre utilizar a extensão de diagnóstico disponível mais recente com todas as novas funcionalidades e correções de erros. 

O *definições* elemento contém as propriedades de configurações para a extensão que podem ser definidas e ler novamente a partir da extensão (algumas vezes referido como configuração pública). O *xmlcfg* propriedade contém com base em xml configuração para os registos de diagnóstico, contadores de desempenho etc que serão recolhidas pelo agente de diagnóstico. Consulte [esquema de configuração de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter mais informações sobre o esquema de xml em si. É uma prática comum para armazenar a configuração xml real como uma variável no modelo Azure Resource Manager e, em seguida, concatenar e base64 codificá-los para definir o valor para *xmlcfg*. Consulte a secção sobre [variáveis de configuração de diagnósticos](#diagnostics-configuration-variables) para saber mais sobre como armazenar o xml em variáveis. O *storageAccount* propriedade especifica o nome da conta de armazenamento para que o diagnóstico serão transferidos dados. 

As propriedades no *protectedSettings* (configuração, por vezes, referida como privado) pode ser definida, mas não é possível ler novamente após a ser definida. A natureza só de escrita do *protectedSettings* torna útil para armazenar segredos como a chave de conta do storage onde os dados de diagnóstico serão escritos.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificar a conta de armazenamento de diagnóstico como parâmetros
O fragmento de json da extensão de diagnóstico acima assume que dois parâmetros *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageResourceGroup* para especificar o armazenamento de diagnóstico conta onde serão armazenados os dados de diagnóstico. Especificar a conta de armazenamento de diagnósticos como um parâmetro torna mais fácil alterar a conta de armazenamento do diagnostics entre ambientes diferentes por exemplo, poderá pretender utilizar uma conta de armazenamento do diagnostics diferentes para fins de teste e outra para a produção implementação.  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
              }
        }

É melhor prática para especificar uma conta de armazenamento de diagnóstico num grupo de recursos diferente do grupo de recursos para a máquina virtual. Um grupo de recursos pode ser considerado como uma unidade de implementação com a respetiva duração, uma máquina virtual pode ser implementada e implementar como novas atualizações de configurações são efetuadas-lo ao mesmo, mas poderá continuar a armazenar os dados de diagnóstico na mesma conta de armazenamento em as implementações de máquina virtual. Com a conta de armazenamento num recurso diferentes permite que a conta de armazenamento aceitar dados a partir de várias implementações de máquinas virtuais, tornando mais fácil solucionar problemas de entre as várias versões.

> [!NOTE]
> Se criar um modelo de máquina virtual do windows a partir do Visual Studio a conta de armazenamento predefinido pode ser definida para utilizar a mesma conta de armazenamento onde o VHD da máquina virtual é carregado. Isto serve para simplificar a configuração inicial da VM. Deverá pesar novamente o modelo a utilizar uma conta de armazenamento diferente que pode ser transmitida como um parâmetro. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variáveis de configuração de diagnóstico
Diagnóstico extensão fragmento json acima define um *accountid* variável para simplificar a obter a chave de conta do storage para o armazenamento do diagnostics:   

    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


O *xmlcfg* propriedade para a extensão de diagnóstico é definida com várias variáveis que são concatenadas em conjunto. Os valores destas variáveis são em xml, pelo que precisam de ser escape corretamente ao definir as variáveis de json.

O seguinte descreve o xml de configuração de diagnóstico que recolhe os contadores de desempenho ao nível do sistema padrão, juntamente com alguns registos de eventos do windows e registos de infraestrutura de diagnóstico. Foi caráter de escape correto e formatado corretamente para que a configuração diretamente pode ser colada na secção de variáveis do seu modelo. Consulte o [esquema de configuração de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter um exemplo legível mais humano o xml de configuração.

        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

O nó de xml de definição de métrica na configuração acima é um elemento de configuração importantes, como define como os contadores de desempenho definido anteriormente no xml no *PerformanceCounter* será agregado e armazenado nó. 

> [!IMPORTANT]
> Estas métricas unidade os gráficos e alertas de monitorização no portal do Azure.  O **métricas** nó com o *resourceID* e **MetricAggregation** tem de ser incluído na configuração de diagnóstico para a VM se quiser ver a VM dados de monitorização o portal do Azure. 
> 
> 

Segue-se um exemplo do xml de definições de métrica: 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

O *resourceID* atributo identifica exclusivamente a máquina virtual na sua subscrição. Certifique-se de que utiliza as funções subscription() e resourceGroup() para que o modelo atualiza automaticamente esses valores com base na subscrição e grupo de recursos que está a implementar.

Se estiver a criar várias máquinas virtuais num ciclo, em seguida, terá de preencher o *resourceID* valor com uma função copyindex () para diferenciar corretamente cada VM individual. O *xmlCfg* valor pode ser atualizado para suportar isto da seguinte forma:  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

O valor MetricAggregation *PT1H* e *PT1M* significam uma agregação ao longo de um minuto e uma agregação mais de uma hora.

## <a name="wadmetrics-tables-in-storage"></a>Tabelas de WADMetrics no armazenamento
A configuração de métricas acima irá gerar tabelas na sua conta de armazenamento de diagnóstico com as seguintes convenções de nomenclatura:

* **WADMetrics** : prefixo padrão para todas as tabelas de WADMetrics
* **PT1H** ou **PT1M** : significa que a tabela contém dados agregados mais de 1 hora ou de 1 minuto
* **P10D** : significa a tabela irá conter os dados de 10 dias do quando a tabela iniciou a recolha de dados
* **V2S** : constante de cadeia
* **AAAAMMDD** : A data em que a tabela iniciou a recolha de dados

Exemplo: *WADMetricsPT1HP10DV2S20151108* irá conter os dados de métricas agregados mais de uma hora para 10 dias, começando no 11-Novembro de 2015    

Cada tabela WADMetrics irá conter as seguintes colunas:

* **PartitionKey**: O partitionkey é criada com base no *resourceID* valor exclusivamente para identificar o recurso VM. para por ex.: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey** : respeita o formato <Descending time tick>:<Performance Counter Name>. O cálculo de marcas de escala de tempo descendente é ticks tempo máximo, menos a hora de início do período de agregação. Por exemplo, Se o período de exemplo iniciado em 10-Novembro de 2015 e 00:00Hrs UTC, em seguida, o cálculo seria: ticks - (DateTime(2015,11,10,0,0,0,DateTimeKind.Utc) novo. Batimentos). Para a memória a chave de linha do contador de desempenho de bytes disponíveis procurará como: 2519551871999999999__:005CMemory:005CAvailable:0020 Bytes
* **CounterName** : É o nome do contador de desempenho. Isto corresponde a *counterSpecifier* definidos na configuração de xml.
* **Máximo** : O valor máximo do contador de desempenho durante o período de agregação.
* **Mínimo** : O valor mínimo de contador de desempenho durante o período de agregação.
* **Total** : A soma de todos os valores de contador de desempenho comunicados ao longo do período de agregação.
* **Contagem** : O número total de valores comunicados para o contador de desempenho.
* **Médio** : O valor médio (contagem/total) do contador de desempenho durante o período de agregação.

## <a name="next-steps"></a>Passos Seguintes
* Para um modelo de exemplo completo da máquina virtual do Windows com a extensão de diagnóstico consulte [201-vm-monitorização--extensão de diagnóstico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Implementar a através do modelo do resource manager [Azure PowerShell](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [linha de comandos do Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Saiba mais sobre [criação de modelos Azure Resource Manager](../../resource-group-authoring-templates.md)

