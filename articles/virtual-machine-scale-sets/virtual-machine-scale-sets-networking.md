---
title: "Trabalhar em rede em conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Propriedades de rede de configuração para conjuntos de dimensionamento de máquinas virtuais do Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: guybo
ms.openlocfilehash: a8520c6d8962cc362fc935f6b515a299c0ce75b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Trabalhar em rede em conjuntos de dimensionamento de máquinas virtuais do Azure

Quando implementa um conjunto de dimensionamento de máquinas virtuais do Azure através do portal, determinadas propriedades de rede são predefinidas, como, por exemplo, um Balanceador de Carga do Azure com regras NAT de entrada. Este artigo descreve como utilizar algumas das funcionalidades de rede mais avançadas que podem ser configuradas em conjuntos de dimensionamento.

Pode configurar todas as funcionalidades abordadas neste artigo através de modelos do Azure Resource Manager. Também estão incluídos exemplos da CLI do Azure e do PowerShell para determinadas funcionalidades. Utilize a CLI 2.10 e o PowerShell 4.2.0 ou posterior.

## <a name="accelerated-networking"></a>Redes Aceleradas
As [Redes Aceleradas](../virtual-network/virtual-network-create-vm-accelerated-networking.md) do Azure melhoram o desempenho da rede ao permitirem a virtualização de E/S de raiz única (SR-IOV) para uma máquina virtual. Para utilizar as redes aceleradas com conjuntos de dimensionamento, defina enableAcceleratedNetworking como **Verdadeiro** nas definições networkInterfaceConfigurations do seu conjunto. Por exemplo:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Criar um conjunto de dimensionamento que faça referência a um balanceador de carga do Azure existente
Quando um conjunto de dimensionamento é criado no portal do Azure, é criado um novo balanceador de carga para a maioria das opções de configuração. Se criar um conjunto de dimensionamento que tenha de referenciar um balanceador de carta existente, pode utilizar a CLI para o fazer. O script de exemplo seguinte cria um balanceador de carta e, depois, um conjunto de dimensionamento, que o referencia:
```bash
az network lb create -g lbtest -n mylb --vnet-name myvnet --subnet mysubnet --public-ip-address-allocation Static --backend-pool-name mybackendpool

az vmss create -g lbtest -n myvmss --image Canonical:UbuntuServer:16.04-LTS:latest --admin-username negat --ssh-key-value /home/myuser/.ssh/id_rsa.pub --upgrade-policy-mode Automatic --instance-count 3 --vnet-name myvnet --subnet mysubnet --lb mylb --backend-pool-name mybackendpool

```

## <a name="configurable-dns-settings"></a>Definições de DNS Configuráveis
Por predefinição, os conjuntos de dimensionamento assumem as definições de DNS específicas da VNet e da sub-rede em que foram criados. Contudo, pode configurar as definições de DNS de um conjunto de dimensionamento diretamente.
~
### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Criar um conjunto de dimensionamento com servidores DNS configuráveis
Para criar um conjunto de dimensionamento com uma configuração DNS personalizada através da CLI 2.0, adicione o argumento **--dns-servers** ao comando **vmss create**, seguido de endereços ip de servidores separados por espaços. Por exemplo:
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Para configurar servidores DNS personalizados num modelo do Azure, adicione uma propriedade dnsSettings à secção networkInterfaceConfigurations do conjunto de dimensionamento. Por exemplo:
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Criar um conjunto de dimensionamento com nomes de domínio de máquinas virtuais configuráveis
Para criar um conjunto de dimensionamento com um nome DNS personalizado para máquinas virtuais com a CLI 2.0, adicione o argumento **--vm-domain-name** ao comando **vmss create**, seguido de uma cadeia que representa o nome de domínio.

Para definir o nome de domínio num modelo do Azure, adicione uma propriedade **dnsSettings** à secção **networkInterfaceConfigurations** do conjunto de dimensionamento. Por exemplo:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

O resultado para um nome dns de máquina virtual individual teria o formato seguinte: 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>IPv4 Público por máquina virtual
Em geral, as máquinas virtuais de conjuntos de dimensionamento do Azure não precisam de ter os seus próprios endereços IP públicos. Na maioria dos cenários, é mais económico e seguro associar um endereço IP público a um balanceador de carga ou a uma máquina virtual individual (também chamada “jumpbox”), que depois encamihna as ligações de entrada às máquinas virtuais do conjunto de dimensionamento, conforme necessário (por exemplo, através de regras NAT de entrada).

No entanto, alguns cenários requerem que as máquinas virtuais do conjunto de dimensionamento tenham os seus próprios endereços IP públicos. Um exemplo de um cenário destes são os jogos, em que uma consola tem de fazer uma ligação direta a uma máquina virtual na cloud, que está a fazer o processamento físico do jogo. Outro exemplo é quando as máquinas virtuais têm de fazer ligações externas a outras em várias regiões numa base de dados distribuída.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Criar um conjunto de dimensionamento com IP público por máquina virtual
Para criar um conjunto de dimensionamento que atribui um endereço IP público a cada máquina virtual com a CLI 2.0, adicione o parâmetro **--public-ip-per-vm** ao comando **vmss create**. 

Para criar um conjunto de dimensionamento com um modelo do Azure, confirme que a versão da API do recurso Microsoft.Compute/virtualMachineScaleSets é, no mínimo, a **2017-03-30** e adicione a propriedade JSON **publicIpAddressConfiguration** à secção ipConfigurations do conjunto. Por exemplo:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Modelo do exemplo: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Consultar os endereços IP públicos das máquinas virtuais num conjunto de dimensionamento
Para listar os endereços IP públicos atribuídos às máquinas virtuais do conjunto de dimensionamento com a CLI 2.0, utilize o comando **az vmss list-instance-public-ips**.

Para utilizar o PowerShell para listar os endereços IP públicos do conjunto de dimensionamento, utilize o comando _Get-AzureRmPublicIpAddress_. Por exemplo:
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Também pode consultar os endereços IP públicos ao referenciar diretamente o ID de recurso da configuração desses endereços. Por exemplo:
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Pode consultar os endereços IP públicos atribuídos às máquinas virtuais do conjunto de dimensionamento com o [Azure Resource Explorer](https://resources.azure.com) ou a API REST do Azure com a versão **2017-03-30** ou superior.

Para utilizar o Resource Explorer para ver os endereços IP públicos de um conjunto de dimensionamento, veja a secção **publicipaddresses** desse conjunto. Por exemplo: https://resources.azure.com/subscriptions/_your_sub_id_/resourceGroups/_your_rg_/providers/Microsoft.Compute/virtualMachineScaleSets/_your_vmss_/publicipaddresses

```
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Exemplo de saída:
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Vários endereços IP por NIC
Todas as NICs ligadas a uma VM dentro de um conjunto de dimensionamento podem ter uma ou mais configurações de IP associadas a si. É atribuído um endereço IP privado a cada configuração. Cada configuração também pode ter um recurso de endereço IP público associado a si. Para compreender quantos endereços IP podem ser atribuídos a uma NIC e quantos pode utilizar numa subscrição do Azure, veja os [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Várias NICs por máquina virtual
Pode ter até 8 NICs por máquina virtual, dependendo do tamanho da máquina. O número máximo de NICs por máquina está disponível no [artigo relativo aos tamanhos das VMs](../virtual-machines/windows/sizes.md). O exemplo seguinte é um perfil de rede de um conjunto de dimensionamento que mostra várias entradas de NICs e múltiplos IPs públicos por máquina virtual:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg-per-scale-set"></a>NSGs por conjunto de dimensionamento
Podem ser aplicados Grupos de Segurança de Rede diretamente a um conjunto de dimensionamento. Para tal, adicione uma referência à secção de configuração da interface de rede das propriedades da máquina virtual do conjunto.

Por exemplo: 
```
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre as redes do Azure, veja [esta documentação](../virtual-network/virtual-networks-overview.md).
