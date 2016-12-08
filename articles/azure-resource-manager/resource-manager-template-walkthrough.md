---
title: "Instruções do Modelo do Azure Resource Manager | Microsoft Docs"
description: "Instruções passo-a-passo para um modelo do Resource Manager que aprovisiona uma arquitetura IaaS do Azure básica."
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: f1cfd704-f6e1-47d5-8094-b439c279c13f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2016
ms.author: navale;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4f541e34e7c0696e4074613c4ab0734a096c6d12
ms.openlocfilehash: 68bec71fd23367a05b405edf9196b28d04fca1e6


---
# <a name="resource-manager-template-walkthrough"></a>Instruções do modelo do Resource Manager
Uma das primeiras questões colocadas ao criar um modelo é “como começar?”. Pode começar com um modelo em branco ao seguir a estrutura básica descrita no [artigo sobre Criação de Modelos](resource-group-authoring-templates.md#template-format) e adicionar os recursos e os parâmetros e variáveis adequados. Uma boa alternativa seria começar por aceder à [galeria de início rápido](https://github.com/Azure/azure-quickstart-templates) e procurar cenários semelhantes ao que está a tentar criar. Pode intercalar vários modelos ou editar um já existente de acordo com o seu cenário específico. 

Vamos observar uma infraestrutura comum:

* Duas máquinas virtuais que utilizam a mesma conta do Storage estão no mesmo conjunto de disponibilidade e estão na mesma sub-rede de uma rede virtual.
* Um único endereço de IP da NIC e VM para cada máquina virtual.
* Um load balancer com uma regra de balanceamento de carga na porta 80

![arquitetura](./media/resource-manager-template-walkthrough/arm_arch.png)

Este tópico explica os passos de criação de um modelo do Resource Manager para essa infraestrutura. O modelo final criado é baseado num modelo de Início rápido denominado [2 VMs num Load Balancer e regras de balanceamento de carga](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

No entanto, como é muita coisa para compilar ao mesmo tempo, vamos criar primeiro uma conta do Storage e implementá-la. Depois de aprender a criar a conta do Storage, irá adicionar os outros recursos e voltar a implementar o modelo para concluir a infraestrutura.

> [!NOTE]
> Pode utilizar qualquer tipo de editor ao criar o modelo. O Visual Studio fornece ferramentas que simplificam o desenvolvimento de modelos, mas não é necessário para concluir este tutorial. Para consultar um tutorial sobre como utilizar o Visual Studio para criar uma implementação de aplicação Web e SQL Database, consulte [Criar e implementar grupos de recursos do Azure através do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 
> 
> 

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager
O modelo é um ficheiro JSON que define todos os recursos que irá implementar. Também permite que defina os parâmetros especificados durante a implementação, as variáveis construídas a partir de outros valores e expressões e as saídas da implementação. 

Vamos começar com o modelo mais simples:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Guarde este ficheiro como **azuredeploy.json** (tenha em atenção que o modelo pode ter qualquer nome, mas deve ser sempre um ficheiro json).

## <a name="create-a-storage-account"></a>Criar uma conta do Storage
Na secção **recursos**, adicione um objeto que defina a conta do Storage, conforme mostrado abaixo. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Pode estar a questionar-se de onde vêm estas propriedades e valores. As propriedades **type**, **name**, **apiVersion** e **location** são elementos padrão que estão disponíveis para todos os tipos de recursos. Pode saber mais sobre os elementos comuns em [Recursos](resource-group-authoring-templates.md#resources). **name** está definido como um valor de parâmetro que passa durante a implementação e **location** como a localização utilizada pelo grupo de recursos. Vamos analisar como se determina **type** e **apiVersion** nas secções abaixo.

A secção **propriedades** contém todas as propriedades que são exclusivas para um tipo de recurso específico. Os valores que especifica nesta secção correspondem exatamente à operação PUT na API REST para criar esse tipo de recurso. Ao criar uma conta do Storage, tem de fornecer um **accountType**. Repare que, na [API REST para criar uma Conta do Storage](https://msdn.microsoft.com/library/azure/mt163564.aspx), a secção de propriedades da operação REST também contém uma propriedade **accountType** e estão documentados os valores permitidos. Neste exemplo, o tipo de conta é definido como **Standard_LRS**, mas pode especificar outro valor ou permitir que os utilizadores passem o tipo de conta como um parâmetro.

Vamos voltar agora à secção **parâmetros** e ver como definir o nome da conta do Storage. Pode saber mais sobre a utilização de parâmetros em [Parâmetros](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Aqui, definiu um parâmetro do tipo de cadeia que irá conter o nome da conta do Storage. O valor para este parâmetro será fornecido durante a implementação do modelo.

## <a name="deploying-the-template"></a>Implementar o modelo
Temos um modelo completo para criar uma nova conta do Storage. Como se recorda, o modelo foi guardado no ficheiro **azuredeploy.json**:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Existem várias formas de implementar um modelo, tal como pode ver no [artigo sobre Implementação de Recursos](resource-group-template-deploy.md). Para implementar o modelo com o Azure PowerShell, utilize:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile azuredeploy.json
```

Em alternativa, para implementar o modelo com a CLI do Azure, utilize:

```azurecli
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Agora é o orgulhoso proprietário de uma conta do Storage!

Os passos seguintes consistirão em adicionar todos os recursos necessários para implementar a arquitetura descrita no início deste tutorial. Irá adicionar estes recursos no mesmo modelo em que tem estado a trabalhar.

## <a name="availability-set"></a>Conjunto de Disponibilidade
Após a definição da conta do Storage, adicione um conjunto de disponibilidade para as máquinas virtuais. Neste caso, não são necessárias propriedades adicionais, pelo que a definição é bastante simples. Consulte a [API REST para criar um Conjunto de Disponibilidade](https://msdn.microsoft.com/library/azure/mt163607.aspx) para ver a secção de propriedades completa, caso pretenda definir os valores da contagem de domínios de atualização e de domínios de falha.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Repare que **name** está definido para o valor de uma variável. Para este modelo, o nome do conjunto de disponibilidade é necessário em alguns locais diferentes. Pode manter o seu modelo mais facilmente se definir esse valor uma vez e o utilizar em vários locais.

O valor que especificar para **type** contém o fornecedor de recursos e o tipo de recurso. Para os conjuntos de disponibilidade, o fornecedor de recursos é **Microsoft.Compute** e o tipo de recurso é **availabilitySets**. Pode obter a lista de fornecedores de recursos disponíveis ao executar o seguinte comando do PowerShell:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Em alternativa, se estiver a utilizar a CLI do Azure, pode executar o seguinte comando:

```azurecli
    azure provider list
```
Tendo em conta que está a criar com contas do Storage, máquinas virtuais e redes virtuais neste tópico, irá trabalhar com:

* Microsoft.Storage
* Microsoft.Compute
* Microsoft.Network

Para ver os tipos de recursos de um fornecedor específico, execute o seguinte comando do PowerShell:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Em alternativa, para a CLI do Azure, o comando seguinte irá devolver os tipos disponíveis no formato JSON e guardá-los num ficheiro.

```azurecli
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Deverá ver **availabilitySets** como um dos tipos em **Microsoft.Compute**. O nome completo do tipo é **Microsoft.Compute/availabilitySets**. Pode determinar o nome do tipo de recurso para qualquer um dos recursos no modelo.

## <a name="public-ip"></a>IP público
Defina um endereço IP público. Mais uma vez, consulte a [API REST para endereços IP públicos](https://msdn.microsoft.com/library/azure/mt163590.aspx) para obter as propriedades a definir.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

O método de alocação está definido como **Dinâmico**, mas pode defini-lo para o valor necessário ou para aceitar um valor de parâmetro. Permitiu que os utilizadores do seu modelo passem um valor para a etiqueta do nome de domínio.

Agora, vamos ver como determinar **apiVersion**. O valor que especifica corresponde simplesmente à versão da API REST que pretende utilizar ao criar o recurso. Por isso, pode consultar a documentação da API REST para esse tipo de recurso. Em alternativa, pode executar o seguinte comando do PowerShell para um determinado tipo.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Este devolve os seguintes valores:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Para ver as versões da API com a CLI do Azure, execute o mesmo comando **azure provider show** apresentado anteriormente.

Ao criar um novo modelo, escolha a versão mais recente da API.

## <a name="virtual-network-and-subnet"></a>Rede virtual e sub-rede
Crie uma rede virtual com uma sub-rede. Consulte a [API REST para redes virtuais](https://msdn.microsoft.com/library/azure/mt163661.aspx) para obter todas as propriedades a definir.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Load balancer
Agora, irá criar um load balancer com acesso externo. Como este load balancer utiliza o endereço IP público, tem de declarar uma dependência no endereço IP público na secção **dependsOn**. Isto significa que o load balancer só será implementado quando a implementação do endereço IP público for concluída. Se não definir esta dependência, receberá um erro, uma vez que o Resource Manager tentará implementar os recursos paralelamente e tentará definir o load balancer para o endereço IP público que ainda não existe. 

Também irá criar um conjunto de endereços de back-end, algumas regras NAT de entrada para o RDP nas VMs e uma regra de balanceamento de carga com uma sonda tcp na porta 80 nesta definição do recurso. Consulte a [API REST para o load balancer](https://msdn.microsoft.com/library/azure/mt163574.aspx) para obter todas as propriedades.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interface de rede
Irá criar duas interfaces de rede, uma para cada VM. Em vez de ter de incluir entradas duplicadas para as interfaces de rede, pode utilizar a [função copyIndex()](resource-group-create-multiple.md) para iterar através do ciclo de cópia (denominado nicLoop) e criar o número de interfaces de rede conforme definido nas variáveis `numberOfInstances`. A interface de rede depende da criação da rede virtual e do load balancer. Esta utiliza a sub-rede definida durante a criação da rede virtual e o ID do load balancer para configurar o conjunto de endereços do load balancer e as regras NAT de entrada.
Consulte a [API REST para interfaces de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) para obter todas as propriedades.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Máquina virtual
Irá criar duas máquinas virtuais, utilizando a função copyIndex(), tal como fez na criação das [interfaces de rede](#network-interface).
A criação de VM depende da conta do Storage, da interface de rede e do conjunto de disponibilidade. Esta VM será criada a partir de uma imagem do marketplace, tal como definido na propriedade `storageProfile` – `imageReference` é utilizado para definir o publicador de imagem, a oferta, o SKU e a versão. Por último, é configurado um perfil de diagnóstico para ativar os diagnósticos para a VM. 

Para localizar as propriedades relevantes para uma imagem do marketplace, siga os artigos [Selecionar imagens da máquina virtual do Linux](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Selecionar imagens da máquina virtual do Windows](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

> [!NOTE]
> Para imagens publicadas por **fornecedores externos**, terá de especificar outra propriedade com o nome `plan`. Pode encontrar um exemplo [neste modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) na galeria de início rápido. 
> 
> 

Terminou de definir os recursos para o modelo.

## <a name="parameters"></a>Parâmetros
Na secção de parâmetros, defina os valores que podem ser especificados ao implementar o modelo. Defina parâmetros apenas para os valores que considera que devem ser diversificados durante a implementação. Pode fornecer um valor predefinido para um parâmetro que é utilizado se não for fornecido um valor durante a implementação. Também pode definir os valores permitidos conforme mostrado para o parâmetro **imageSKU**.

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variáveis
Na secção de variáveis, pode definir valores que são utilizados em mais do que um local no seu modelo ou valores que são construídos a partir de outras expressões ou variáveis. As variáveis são frequentemente utilizadas para simplificar a sintaxe do modelo.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Concluiu o modelo! Pode comparar o seu modelo com o modelo completo na [galeria de início rápido](https://github.com/Azure/azure-quickstart-templates) no [modelo de 2 VMs com load balancer de carga e regras do load balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules). O seu modelo pode ser ligeiramente diferente consoante a utilização de números de versão diferentes. 

Pode implementar novamente o modelo ao utilizar os mesmos comandos que utilizou durante a implementação da conta do Storage. Não é necessário eliminar a conta do Storage antes de voltar a implementar, pois o Resource Manager irá ignorar a nova criação dos recursos que já existem e não foram alterados.

## <a name="next-steps"></a>Passos seguintes
* [O Visualizador de Modelos do Azure Resource Manager](http://armviz.io/#/) é uma ótima ferramenta para visualizar os modelos do Resource Manager, dado que estes podem ficar demasiado grandes para compreender apenas ao ler o ficheiro json.
* Para saber mais sobre a estrutura de um modelo, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre a implementação de um modelo, consulte [Implementar um Grupo de Recursos com um modelo do Azure Resource Manager](resource-group-template-deploy.md)
* Para uma série de quatro partes sobre automatizar a implementação, consulte [Automating application deployments to Azure Virtual Machines (Automatizar implementações de aplicações para Máquinas Virtuais do Azure)](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta série abrange arquitetura de aplicação, acesso e segurança, disponibilidade e dimensionamento, e implementação de aplicação.




<!--HONumber=Nov16_HO3-->


