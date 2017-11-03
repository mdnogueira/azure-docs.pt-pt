---
title: "Máquinas virtuais num modelo Azure Resource Manager | Microsoft Azure"
description: "Saiba mais sobre como o recurso de máquina virtual está definido num modelo Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: davidmu
ms.openlocfilehash: 9c0039987ec28601c9338d2b94633c38c31e01f8
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Máquinas virtuais num modelo Azure Resource Manager

Este artigo descreve os aspetos de um modelo Azure Resource Manager que se aplicam a máquinas virtuais. Este artigo não descreve um modelo completo para a criação de uma máquina virtual; para que precisa de definições de recursos para as contas do storage, interfaces de rede, endereços IP públicos e redes virtuais. Para obter mais informações sobre como estes recursos podem ser definidos em conjunto, consulte o [instruções do modelo do Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Existem muitos [modelos na galeria](https://azure.microsoft.com/documentation/templates/?term=VM) que incluam o recurso VM. Nem todos os elementos que podem ser incluídos num modelo são descritos aqui.

Este exemplo mostra uma secção de recurso típica de um modelo para criar um número especificado de VMs:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Neste exemplo baseia-se numa conta de armazenamento que foi criada anteriormente. Pode criar a conta de armazenamento implementando-a partir do modelo. O exemplo também depende de uma interface de rede e os respetivos recursos dependentes que teriam de ser definidos no modelo. Estes recursos não são mostrados no exemplo.
>
>

## <a name="api-version"></a>Versão de API

Quando implementar recursos através de um modelo, terá de especificar uma versão de API a utilizar. O exemplo mostra o recurso de máquina virtual com este elemento apiVersion:

```
"apiVersion": "2016-04-30-preview",
```

A versão da API que especificar no seu modelo afeta as propriedades que pode definir no modelo. Em geral, deve selecionar a versão mais recente da API durante a criação de modelos. Os modelos existentes, pode decidir se pretende continuar a utilizar uma versão de API anterior ou atualizar o modelo para a versão mais recente tirar partido das novas funcionalidades.

Utilize estas oportunidades para obter as versões de API mais recentes:

- API de REST - [lista todos os fornecedores de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)
- PowerShell – [Get AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider)
- CLI do Azure 2.0 - [mostrar de fornecedor az](https://docs.microsoft.com/cli/azure/provider#az_provider_show)

## <a name="parameters-and-variables"></a>Os parâmetros e variáveis

[Os parâmetros](../../resource-group-authoring-templates.md) torna mais fácil para especificar valores para o modelo quando executa-o. Esta secção de parâmetros é utilizada no exemplo:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Quando implementar o modelo de exemplo, introduza valores para o nome e a palavra-passe da conta de administrador em cada VM e o número de VMs para o criar. Tem a opção de especificar valores de parâmetro num ficheiro separado que é gerido com o modelo ou fornecendo valores, quando lhe for pedido.

[Variáveis](../../resource-group-authoring-templates.md) torna mais fácil para configurar os valores no modelo que são utilizados repetidamente ao longo, ou que pode alterar ao longo do tempo. Esta secção de variáveis é utilizada no exemplo:

```
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Quando implementar o modelo de exemplo, os valores das variáveis são utilizados para o nome e identificador de conta de armazenamento criado anteriormente. As variáveis são também utilizadas para fornecer as definições para a extensão de diagnóstico. Utilize o [melhores práticas para a criação de modelos Azure Resource Manager](../../resource-manager-template-best-practices.md) para ajudar a decidir como pretende estrutura os parâmetros e variáveis do seu modelo.

## <a name="resource-loops"></a>Ciclos de recursos

Quando precisar de mais do que uma máquina virtual para a sua aplicação, pode utilizar um elemento de copiar um modelo. Este elemento opcional repete-se através de criar o número de VMs que especificou como um parâmetro:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Além disso, repare no exemplo que o índice do ciclo é utilizado durante a especificação de alguns dos valores para o recurso. Por exemplo, se introduziu uma contagem de instâncias de três, os nomes dos discos de sistema operativo são myOSDisk1, myOSDisk2 e myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Este exemplo utiliza discos geridos para as máquinas virtuais.
>
>

Tenha em atenção que a criação de um ciclo para um recurso no modelo pode necessitar de utilizar o ciclo quando criar ou aceder a outros recursos. Por exemplo, várias VMs não é possível utilizar a mesma interface de rede, pelo que o se o seu modelo repete através da criação de três VMs, deve também percorrer criar três interfaces de rede. Ao atribuir uma interface de rede a uma VM, o índice do ciclo é utilizado para identificá-lo:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Dependências

A maioria dos recursos dependem de outros recursos para funcionar corretamente. Máquinas virtuais tem de estar associadas uma rede virtual e fazer que necessita de uma interface de rede. O [dependsOn](../../resource-group-define-dependencies.md) elemento é utilizado para se certificar de que a interface de rede está pronta para ser utilizada antes das VMs que são criadas:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Gestor de recursos implementa em paralelo quaisquer recursos que não são dependentes de outro recurso que está a ser implementado. Tenha cuidado ao definir as dependências, porque inadvertidamente pode abrandar a implementação, especificando dependências desnecessárias. Dependências podem estar ligados através de vários recursos. Por exemplo, a interface de rede depende do endereço IP público e recursos de rede virtual.

Como pode saber se é necessária uma dependência? Observe os valores definidos no modelo. Se um elemento os pontos de definição do recurso de máquina virtual para outro recurso que é implementada no mesmo modelo, terá uma dependência. Por exemplo, a máquina virtual de exemplo define um perfil de rede:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Para definir esta propriedade, a interface de rede tem de existir. Por conseguinte, tem uma dependência. Também tem de definir uma dependência quando um recurso (um subordinado) está definido no recurso de outro (principal). Por exemplo, as definições de diagnóstico e extensões de script personalizado estão ambas definidas como recursos subordinados da máquina virtual. Não é possível criar até que a máquina virtual existe. Por conseguinte, os dois recursos são marcados como dependentes na máquina virtual.

## <a name="profiles"></a>Perfis

Vários elementos de perfil são utilizados para definir um recurso de máquina virtual. Algumas são necessárias e algumas são opcionais. Por exemplo, os elementos o hardwareProfile, osProfile, storageProfile e networkProfile são necessários, mas o diagnosticsProfile é opcional. Estes perfis definem definições, tais como:
   
- [tamanho](sizes.md)
- [nome](/architecture/best-practices/naming-conventions) e as credenciais
- disco e [definições do sistema operativo](cli-ps-findimage.md)
- [interface de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Diagnóstico de arranque

## <a name="disks-and-images"></a>Discos e imagens
   
No Azure, podem representar os ficheiros vhd [discos ou nas imagens](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando o sistema operativo num ficheiro vhd é especializado para uma VM específica, é referido como um disco. Quando o sistema operativo num ficheiro vhd está generalizado para ser utilizado para criar várias VMs, é referido como uma imagem.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Criar novas máquinas virtuais e discos de novo a partir de uma imagem de plataforma

Quando cria uma VM, tem de decidir que utilizam o sistema operativo. O elemento de imageReference é utilizado para definir o sistema operativo de uma nova VM. O exemplo mostra uma definição para um sistema operativo Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Se pretender criar um sistema operativo Linux, pode utilizar esta definição:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Definições de configuração para o disco do sistema operativo são atribuídas com o elemento de osDisk. O exemplo define um novo disco gerido com o modo de colocação em cache definido como **ReadWrite** e de que o disco está a ser criado a partir de um [imagem de plataforma](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Criar novas máquinas virtuais a partir de discos geridos existentes

Se pretender criar máquinas virtuais a partir de discos existentes, remova o atributo imageReference e os elementos de osProfile e definir estas definições de disco:

```
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Criar novas máquinas virtuais a partir de uma imagem gerida

Se pretender criar uma máquina virtual a partir de uma imagem gerida, altere o elemento de imageReference e definir estas definições de disco:

```
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Anexe discos de dados

Opcionalmente, pode adicionar os discos de dados para as VMs. O [número de discos](sizes.md) depende do tamanho do disco do sistema operativo que utilizar. Com o tamanho das VMs definido como Standard_DS1_v2, o número máximo de discos de dados que foi adicionado o lhes é dois. No exemplo, um disco de dados geridos está a ser adicionado a cada VM:

```
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Extensões

Embora [extensões](extensions-features.md) são um recurso separado, estão diretamente associadas a VMs. As extensões podem ser adicionadas como um recurso de subordinados da VM ou como um recurso separado. O exemplo mostra o [extensão de diagnóstico](extensions-diagnostics-template.md) a ser adicionados às VMs:

```
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Este recurso de extensão utiliza a variável de storageName e as variáveis de diagnóstico para fornecer valores. Se pretender alterar os dados que são recolhidos por esta extensão, pode adicionar mais contadores de desempenho para a variável de wadperfcounters. Também pode optar por colocar os dados de diagnóstico para uma conta de armazenamento diferente onde estão armazenados os discos da VM.

Existem várias extensões que pode instalar numa VM, mas é mais útil provavelmente o [extensão de Script personalizado](extensions-customscript.md). No exemplo, executa um script do PowerShell com o nome start.ps1 em cada VM quando iniciar pela primeira vez:

```
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

O script de start.ps1 pode realizar muitas tarefas de configuração. Por exemplo, os discos de dados que são adicionados às VMs no exemplo não foram inicializados; Pode utilizar um script personalizado para inicializá-los. Se tiver várias tarefas de arranque para o fazer, pode utilizar o ficheiro start.ps1 chamar outros scripts do PowerShell no armazenamento do Azure. O exemplo utiliza o PowerShell, mas pode utilizar qualquer método de script que está disponível no sistema operativo que está a utilizar.

Pode ver o estado das extensões instaladas das definições de extensões no portal:

![Obter o estado da extensão](./media/template-description/virtual-machines-show-extensions.png)

Também pode obter as informações de extensão utilizando o **Get-AzureRmVMExtension** comando do PowerShell, o **get de extensão de vm** comando 2.0 de CLI do Azure, ou o **obter as informações de extensão** REST API.

## <a name="deployments"></a>Implementações

Quando implementar um modelo, o Azure controla os recursos que implementou como um grupo e atribui automaticamente um nome para este grupo implementado. O nome da implementação é igual ao nome do modelo.

Se estiver com curiosidade sobre o estado de recursos na implementação, pode utilizar o painel do grupo de recursos no portal do Azure:

![Obter informações de implementação](./media/template-description/virtual-machines-deployment-info.png)
    
Não é um problema ao utilizar o mesmo modelo para criar recursos ou para atualizar recursos existentes. Quando utiliza comandos para implementar modelos, terá a oportunidade para indicar que [modo](../../resource-group-template-deploy.md) que pretende utilizar. O modo pode ser definido para o **concluída** ou **Incremental**. A predefinição é fazer atualizações incrementais. Seja cuidadoso ao utilizar o **concluída** modo porque acidentalmente pode eliminar recursos. Quando definir o modo como **concluída**, Gestor de recursos elimina todos os recursos no grupo de recursos que não estão no modelo.

## <a name="next-steps"></a>Passos Seguintes

- Criar os seus próprios modelos utilizando [modelos Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).
- Implementar o modelo que criou utilizando [criar máquina virtual do Windows com um modelo do Resource Manager](ps-template.md).
- Saiba como gerir as VMs que criar ao rever [criar e gerir VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
