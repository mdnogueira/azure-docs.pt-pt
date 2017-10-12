---
title: "Configurar pontos finais do serviço de rede virtual do Azure | Microsoft Docs"
description: "Saiba como ativar e desativar pontos finais de serviço na sua rede virtual"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: 9c953e697d5d1c6e53df8eaf72c4e4fbd83ca286
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-virtual-network-service-endpoints"></a>Configurar Pontos Finais do Serviço de Rede Virtual

Os pontos finais do serviço de rede virtual (VNet) permitem-lhe obter os recursos de serviço do Azure na sua Rede Virtual do Azure, removendo totalmente o acesso a estes recursos via Internet. Os pontos finais de serviço fornecem ligação direta a um serviço do Azure a partir da rede virtual, permitindo-lhe utilizar o espaço de endereço privado da sua VNet para aceder aos serviços do Azure. O tráfego destinado aos serviços do Azure através de pontos finais de serviço permanece sempre na rede backbone do Microsoft Azure. Saiba mais sobre ["Pontos finais dE serviço de rede virtual"](virtual-network-service-endpoints-overview.md)

Este artigo indica os passos para ativar e desativar pontos finais de serviço. Assim que os pontos finais forem ativados numa sub-rede para um serviço do Azure, pode obter recursos específicos do serviço para uma rede virtual.

Os pontos finais de serviço podem ser configurados no [portal do Azure](#azure-portal), no [Azure PowerShell](#azure-powershell), na [interface de linha de comandos do Azure](#azure-cli) ou num [ modelo](#resource-manager-template) do Azure Resource Manager.

>[!NOTE]
Durante a pré-visualização, a funcionalidade de pontos finais de serviço da Vnet é suportada para regiões específicas. Para obter a lista de regiões suportadas, consulte a página [Atualizações da Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="service-endpoint-configuration-overview"></a>Descrição geral da configuração de pontos finais de serviço

- Os pontos finais de serviço só podem ser configurados em VNets implementadas através do modelo de implementação do Azure Resource Manager.

- Os pontos finais de serviço são definidos em cada sub-rede de uma VNet.

- Para uma sub-rede, só pode configurar um ponto final de serviço para um serviço. Pode configurar vários pontos finais de serviço para diferentes serviços (por exemplo, Armazenamento do Microsoft Azure, SQL do Azure).

- Pode ativar os pontos finais numa sub-rede nova ou existente.

- A localização é configurada automaticamente para um ponto final. Por predefinição, os pontos finais de serviço são configurados para a região da VNet. Para o Armazenamento do Microsoft Azure, para suportar cenários de ativação pós-falha, os pontos finais são configurados automaticamente para [regiões emparelhadas do Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)

  >[!NOTE]
  Dependendo do tamanho da sub-rede/VNet, a ativação do ponto final de serviço pode levar algum tempo a ser concluída. Certifique-se de que não existem tarefas críticas em curso quando ativar pontos finais de serviço. Os pontos finais de serviço mudam de rota em cada NIC na sua sub-rede e podem terminar em ligações TCP abertas. 

- A chamada de ponto final de serviço devolve "com êxito" depois de os fluxos de tráfego para o serviço em todas as NICs na sub-rede terem sido comutadas para os endereços IP privados da Vnet.

- Rotas efetivas para validar a configuração do ponto final:

   Para validar se o ponto final de serviço está configurado corretamente, as "rotas efetivas" em qualquer NIC na sub-rede mostram uma nova rota "predefinida" com nextHopType: VirtualNetworkServiceEndpoint por serviço e região. Saiba mais sobre [resolver problemas com rotas efetivas](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   As rotas efetivas só podem ser visualizadas se tiver uma ou mais interfaces de rede (NICs) configuradas e associadas a uma máquina virtual em execução na sub-rede.

## <a name="azure-portal"></a>Portal do Azure

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Configurar o ponto final de serviço numa sub-rede durante a Criação da VNet

1. Abre o [Portal do Azure](https://portal.azure.com/).
Inicie sessão no Azure com a sua conta do Azure. Se não tiver uma conta do Azure, pode inscrever-se para obter uma avaliação gratuita. A conta deve ter as [permissões](#provisioning) necessárias para criar uma rede virtual e o ponto final de serviço.
2. Clique em + Novo > Rede > Rede virtual > + Adicionar.
3. Em "Criar rede virtual", introduza os seguinte valores e clique em Criar:

Definição | Valor
------- | -----
Nome    | myVnet
Espaço de endereços | 10.0.0.0/16
Nome da sub-rede|mySubnet
Intervalo de endereços da sub-rede|10.0.0.0/24
Grupo de recursos|Deixe a opção Criar novo selecionada e, em seguida, introduza um nome.
Localização|É suportada qualquer região; por exemplo, Leste da Austrália
Subscrição|Selecione a sua subscrição.
__ServiceEndpoints__|Ativado
__Serviços__ | Selecione um ou todos os serviços disponíveis. Durante a pré-visualização, os serviços suportados são: __"Microsoft", "Microsoft.Sql"__.

Selecione os serviços para os pontos finais: ![Selecionar Serviços dos Pontos Final de Serviço](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. Valide se todas as definições estão corretas e clique em "Criar".

![Definir ponto final de serviço](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Para concluir a proteção dos recursos de serviço do Azure para a sua VNet, clique na documentação do serviço em [Passos seguintes](#Next%20Steps).


### <a name="validating-service-endpoint-configuration"></a>Validar configuração de pontos finais de serviço

Confirme se os pontos finais de serviço estão configurados ao utilizar os seguintes passos:

- Nos recursos, clique em "Redes virtuais". Pesquise a VNet.
- Clique no nome da Vnet e navegue para "Pontos finais de serviço"
- Os pontos finais configurados apresentam "Com êxito". Também é possível ver as localizações configuradas automaticamente

![Confirmar a Configuração dos Pontos Finais de Serviço](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Rotas efetivas para validar a configuração do ponto final

Para ver as rotas efetivas numa interface de rede (NIC) na sub-rede, clique em qualquer NIC nessa sub-rede. Em "Suporte + Resolução de problemas", clique em "Rotas efetivas". Se o ponto final estiver configurado, verá uma nova rota "predefinida" com prefixos de endereço do serviço como destino e nextHopType como "VirtualNetworkServiceEndpoint".

![Rotas efetivas para pontos finais de serviço](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Configurar pontos finais de serviço para sub-redes existentes numa VNet

1. Nos recursos, clique em "Redes virtuais" e pesquise uma VNet existente
2. Clique no nome da Vnet e navegue para "Pontos finais de serviço"
3. Clique em "Adicionar". Selecione "Serviço". Pode criar um ponto final para apenas um serviço de cada vez. 
4. Selecione todas as sub-redes em que pretende aplicar o ponto final. Clique em "Adicionar"

![Configuração de pontos finais de serviço](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Eliminar pontos finais de serviço
1. Nos recursos, clique em "Redes virtuais". Pesquise uma VNet existente ao filtrar pelo nome da VNet.
2. Clique no nome da Vnet e navegue para "Pontos finais de serviço"
3. Clique no nome do serviço e clique com o botão direito do rato na entrada de ponto final de serviço
4. Selecione "Eliminar"

![Eliminação do Ponto Final de Serviço](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure PowerShell

Pré-requisitos da configuração:

- Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Para iniciar uma sessão do PowerShell, aceda a **Iniciar**, introduza **powershell** e, em seguida, clique em **PowerShell**.
- No PowerShell, inicie sessão no Azure introduzindo o comando `login-azurermaccount`. A conta deve ter as [permissões](#provisioning) necessárias para criar uma rede virtual e o ponto final de serviço.

### <a name="get-available-service-endpoints-for-azure-region"></a>Obter pontos finais de serviço disponíveis para a região do Azure

Utilize o comando abaixo para obter a lista dos serviços suportados para pontos finais, para uma região do Azure.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Saída: 
Nome | ID | Tipo
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/Providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/Providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Adicionar o ponto final de serviço do Armazenamento do Microsoft Azure a uma sub-rede *mySubnet* ao criar a rede virtual *myVNet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
Pode ativar vários serviços utilizando a lista de valores separados por vírgulas de nomes de serviço.
Exemplo: "Microsoft.Storage", "Microsoft.Sql"

Saída Esperada:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Para concluir a proteção dos recursos de serviço do Azure para a sua VNet, clique na documentação do serviço em [Passos seguintes](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Adicionar vários pontos finais de serviço a uma sub-rede existente

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Saída Esperada: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Ver pontos finais de serviço configurados numa sub-rede

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Saída:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Eliminar pontos finais de serviço numa sub-rede
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>CLI do Azure

Pré-requisitos da configuração:
- Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã. Para obter mais informações sobre o início de sessão, veja [Introdução à CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
 - A conta deve ter as [permissões](#provisioning) necessárias para criar uma rede virtual e o ponto final de serviço.

 Para obter uma lista completa dos comandos para as redes virtuais, consulte [Comandos de Rede Virtual do CLI do Azure](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)

### <a name="get-available-service-endpoints-for-azure-region"></a>Obter pontos finais de serviço disponíveis para a região do Azure

Utilize o comando abaixo para obter a lista dos serviços suportados para pontos finais para uma região do Azure; por exemplo, "EUA Ocidental".
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Saída:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Adicionar o ponto final de serviço do Armazenamento do Microsoft Azure a uma sub-rede *mySubnet* ao criar a rede virtual *myVNet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Para adicionar vários pontos finais: --service-endpoints Microsoft.Storage Microsoft.Sql

Saída:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Para concluir a proteção dos recursos de serviço do Azure para a sua VNet, clique na documentação do serviço em [Passos seguintes](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Adicionar vários pontos finais de serviço a uma sub-rede existente

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Saída Esperada:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Ver pontos finais de serviço configurados numa sub-rede

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Eliminar pontos finais de serviço numa sub-rede
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Saída: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager

### <a name="securing-azure-service-resources-to-vnets"></a>Obter recursos de serviço do Azure para VNets

Pode obter recursos do Azure específicos para a sua rede virtual através de pontos finais de serviço.

Transfira o [modelo do Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) de exemplo para obter uma conta de armazenamento para uma sub-rede numa VNet.

O modelo cria uma VNet com 2 sub-redes, uma VM com uma NIC em cada uma das sub-redes. Ativa o ponto final numa sub-rede e obtém uma conta de armazenamento para essa sub-rede.

Pode transferir o modelo e modificar os elementos do mesmo para o ajustar ao seu cenário.

O modelo inclui instruções para a sua implementação utilizando o portal do Azure, o PowerShell ou o CLI do Azure. Certifique-se de que tem as [permissões](#provisioning) necessárias para configurar o ponto final e obter a conta.

Para obter recursos do Azure para uma sub-rede:

- um ponto final de serviço deve ser configurado nessa sub-rede
- o recurso deve obtido para a vnet através da adição de uma regra de rede virtual ao recurso.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Eliminar pontos finais de serviço com recursos obtidos para a sub-rede
Se os recursos de serviço do Azure forem obtidos para a sub-rede e o ponto final de serviço for eliminado, deixará de poder aceder ao recurso a partir da sub-rede.
Apenas reativar o ponto final não irá restaurar o acesso aos recursos anteriormente obtidos para a sub-rede.

Para voltar a obter o recurso de serviço para esta sub-rede, tem de:

- voltar a ativar o ponto final
- remover a regra de vnet antiga do recurso
- adicionar uma nova regra de obtenção do recurso para a sub-rede

## <a name="provisioning"></a>Aprovisionamento

Os pontos finais de serviço podem ser configurados de forma independente por um utilizador com acesso de escrita para a rede virtual em redes virtuais.

Para obter os recursos de serviço do Azure para uma VNet, o utilizador tem de ter permissão para "Microsoft.Network/JoinServicetoaSubnet" para as sub-redes que estão a ser adicionadas. Esta permissão está incluída por predefinição nas funções incorporadas de administrador de serviço e podem ser modificadas mediante a criação de funções personalizadas.

Saiba mais sobre [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles).

As VNets e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se pertencerem a subscrições diferentes, os recursos devem existir abaixo do inquilino do Active Directory (AD) aquando desta pré-visualização.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais instruções de obtenção do recurso de serviço para VNets, consulte as ligações abaixo:

[Obter contas do Armazenamento do Microsoft Azure para Redes Virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Obter o SQL do Azure para Redes Virtuais](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
