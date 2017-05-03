---
title: 'Ligar a rede no local a uma rede virtual do Azure: Rede de VPNs: CLI | Microsoft Docs'
description: "Passos para criar uma ligação IPsec da sua rede no local a uma rede virtual do Azure através da Internet pública. Estes passos ajudam-no a criar uma ligação de Gateway de Rede de VPNs em vários sites com a CLI."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Criar uma rede virtual com uma ligação de Rede de VPNs através da CLI

Este artigo mostra-lhe como utilizar a CLI do Azure para criar uma ligação de gateway de Rede de VPNs a partir da sua rede no local para a VNet. Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Clássica – portal do Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clássica – portal clássico](vpn-gateway-site-to-site-create.md)
> 
>


![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre o gateways de VPN, veja [About VPN gateway (Acerca do gateway de VPN)](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Certifique-se de que pretende trabalhar com o modelo de implementação Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Um dispositivo VPN compatível e alguém que consiga configurá-lo. Para obter mais informações sobre os dispositivos VPN compatíveis e a configuração do dispositivo, consulte [About VPN Devices (Acerca dos Dispositivos VPN)](vpn-gateway-about-vpn-devices.md).
* Um endereço IP IPv4 público com acesso exterior para o seu dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. Ao criar esta configuração, tem de especificar prefixos de intervalo de endereços IP que o Azure irá encaminhar para a sua localização no local. Nenhuma das sub-redes da rede local pode sobrepor as sub-redes de rede virtual a que pretende ligar. 
* A versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalar a CLI 2.0 do Azure).

### <a name="example-values"></a>Valores de exemplo

Pode utilizar os valores seguintes para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

Se tiver mais de uma subscrição do Azure, liste as subscrições da conta.

```azurecli
Az account list --all
```

Especifique a subscrição que pretende utilizar.

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>2. Criar um grupo de recursos

O exemplo seguinte cria um grupo de recursos com o nome “TestRG1” na localização “eastus”. Se já tiver um grupo de recursos na região em que pretende criar a sua VNet, pode utilizá-lo.

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3. Criar uma rede virtual

Se ainda não tiver uma rede virtual, crie uma. Quando criar uma rede virtual, confirme que os espaços de endereços que especificar não se sobrepõem a nenhum dos espaços de endereços que tem na sua rede no local. 

O exemplo seguinte cria uma rede virtual com o nome “TestVNet1” e uma sub-rede, “Subnet1”.

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4. <a name="gwsub"></a>Criar a sub-rede de gateway

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

Para esta configuração, também precisa de uma sub-rede de gateway. O gateway da rede virtual utiliza uma sub-rede do gateway que contém os endereços IP que os serviços do gateway de VPN utilizam. Quando cria uma sub-rede de gateway, esta tem de ter o nome “GatewaySubnet”. Se lhe der outro nome, está a criar uma sub-rede, mas o Azure não a tratará como uma sub-rede de gateway.

O tamanho da sub-rede de gateway que especificar depende da configuração do gateway de VPN que pretende criar. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclui mais endereços selecionando /27 ou /28. Utilizar uma sub-rede de gateway maior permite que os endereços IP suficientes suportem possíveis configurações futuras.


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. Criar o gateway de rede local

O gateway de rede local refere-se normalmente à sua localização no local. Dê um nome ao site pelo qual o Azure se possa referir ao mesmo e especifique o endereço IP do dispositivo VPN no local para o qual vai criar uma ligação. Também pode especificar os prefixos do endereço IP que vai ser encaminhado através do gateway de VPN para o dispositivo VPN. Os prefixos do endereço que especificar são os que estão localizados na sua rede no local. Se a rede no local se alterar, pode atualizar facilmente os prefixos.

Utilize os seguintes valores:

* O *--gateway-ip-address* é o endereço IP do seu dispositivo VPN no local. O dispositivo VPN não pode estar localizado atrás de um NAT.
* Os *--local-address-prefixes* são os seus espaços de endereços no local.

O exemplo seguinte mostra como adicionar um gateway de rede local com vários prefixos de endereço:

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Solicitar um endereço IP público

Peça um endereço IP público que será alocado ao gateway de VPN da rede virtual. Este é o endereço IP ao qual o dispositivo VPN vai ser configurado para se ligar.

Atualmente o gateway da rede virtual do modelo de implementação Resource Manager só suporta endereços IP públicos com o método Alocação Dinâmica. No entanto, não significa que o endereço IP é alterado. O endereço IP do gateway de VPN só é alterado quando o gateway é eliminado e recriado. O endereço IP público do gateway da rede virtual não é alterado ao redimensionar, repor ou realizar qualquer outra manutenção/atualização interna do gateway de VPN. 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Criar o gateway de VPN

Crie o gateway de VPN da rede virtual. A criação de um gateway de VPN pode demorar 45 minutos ou mais a ser concluída.

Utilize os seguintes valores:

* O *--gateway-type* para uma configuração de Rede de VPNs é *Vpn*. O tipo de gateway é sempre específico da configuração que estiver a implementar. Para obter mais informações, veja [Gateway types](vpn-gateway-about-vpn-gateway-settings.md#gwtype) (Tipos de gateways).
* O *--vpn-type* pode ser *RouteBased* (conhecido como Gateway Dinâmico em alguma documentação) ou *PolicyBased* (referido como Gateway Estático em alguma documentação). A definição é específica de requisitos do dispositivo ao qual se está a tentar ligar. Para obter mais informações sobre os tipos de gateway de VPN, veja [About VPN Gateway configuration settings](vpn-gateway-about-vpn-gateway-settings.md#vpntype) (Acerca das definições de configuração do Gateway de VPN).
* O *--sku* pode ser Básico, Standard ou HighPerformance. Existem limitações de configuração para determinados SKUs. Para obter mais informações, veja [SKUs de gateway](vpn-gateway-about-vpngateways.md#gateway-skus).

Depois de executar este comando, não verá nenhum feedback nem resultados. A criação de um gateway demora cerca de 45 minutos.

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8. Configurar o dispositivo VPN

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
  Para encontrar o endereço IP Público do gateway de rede virtual, utilize o exemplo seguinte, substituindo os valores pelos seus próprios valores. Para maior facilidade de leitura, a saída é formatada para apresentar a lista de IPs públicos no formato de tabela.

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9. Criar a ligação VPN

Crie a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN no local. Tenha particular atenção ao valor de chave partilhada, que tem de corresponder ao valor de chave partilhada configurado para o seu dispositivo VPN.

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Após uns breves instantes, a ligação será estabelecida.

## <a name="toverify"></a>10. Verificar a ligação VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

Se quiser utilizar outro método para verificar a sua ligação, veja [Verify a VPN Gateway connection](vpn-gateway-verify-connection-resource-manager.md) (Verificar ligações de Gateway de VPN).

## <a name="common-tasks"></a>Tarefas comuns

### <a name="to-view-local-network-gateways"></a>Para ver os gateways de rede local

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>Para modificar os prefixos de endereços IP de um gateway de rede local
Se precisar de alterar os prefixos do gateway de rede local, utilize as instruções seguintes. Sempre que fizer uma alteração, tem de ser especificada a lista completa de prefixos, não apenas aqueles que pretende alterar.

- **Se tiver uma ligação especificada**, utilize o exemplo abaixo. Especifique a lista completa de prefixos, que consiste em prefixos existentes e naqueles que quer adicionar. Neste exemplo, 10.0.0.0/24 e 20.0.0.0/24 já estão presentes. Adicionamos os prefixos 30.0.0.0/24 e 40.0.0.0/24.

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **Se não tiver uma ligação especificada**, utilize o mesmo comando que utilizou para criar um gateway de rede local. Também pode utilizar este comando para atualizar o endereço IP do gateway para o dispositivo VPN. Utilize este comando apenas se ainda não tiver uma ligação. Neste exemplo, 10.0.0.0/24, 20.0.0.0/24, 30.0.0.0/24 e 40.0.0.0/24 estão presentes. Especificamos apenas os prefixos que queremos manter. Neste caso, 10.0.0.0/24 e 20.0.0.0/24.

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>Para modificar o endereço IP do gateway de um gateway de rede local

O endereço IP desta configuração é o endereço IP do dispositivo VPN para o qual está a criar uma ligação. Se o endereço IP do dispositivo VPN se alterar, pode modificar o valor. O endereço IP pode ser alterado, mesmo se existir uma ligação de gateway.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

Quando vir o resultado, verifique que os prefixos do endereço IP estão incluídos.

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>Para ver o endereço IP público do gateway da rede virtual

Para encontrar o endereço IP público do gateway da rede virtual, utilize o exemplo seguinte. Para maior facilidade de leitura, a saída é formatada para apresentar a lista de IPs públicos no formato de tabela.

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>Para verificar os valores de chave partilhada

Verifique se o valor da chave partilhada é igual ao valor que utilizou na configuração do dispositivo VPN. Se não for, execute a ligação novamente com o valor do dispositivo ou atualize o dispositivo com o valor que é devolvido. Os valores têm de corresponder.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>Passos seguintes

*  Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Para obter informações sobre o túnel forçado, veja [Configure forced tunneling](vpn-gateway-forced-tunneling-rm.md) (Configurar o túnel forçado).
* Para obter uma lista dos comandos da CLI do Azure de rede, veja [Azure CLI](https://docs.microsoft.com/cli/azure/network) (CLI do Azure).
