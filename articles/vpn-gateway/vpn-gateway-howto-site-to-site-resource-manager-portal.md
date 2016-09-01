<properties
   pageTitle="Criar uma rede virtual com uma ligação de Rede de VPNs utilizando o Azure Resource Manager e o Portal do Azure | Microsoft Azure"
   description="Como criar uma VNet com o modelo Resource Manager e como ligá-la à sua rede local no local através de uma ligação de gateway de VPN S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc"/>

# Criar uma VNet com uma ligação de Rede de VPNs utilizando o Portal do Azure e o Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portal Clássico do Azure](vpn-gateway-site-to-site-create.md)
- [PowerShell – Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Este artigo explica como criar uma rede virtual e uma ligação de Rede de VPNs à sua rede no local utilizando o modelo de implementação Azure Resource Manager e o Portal do Azure.

![Diagrama](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)



### Modelos de implementação e ferramentas para ligações Site a Site

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)] 

Se pretender ligar VNets entre si, mas não estiver a criar uma ligação para uma localização no local, veja [Configurar uma ligação VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md).

## Antes de começar

Antes de iniciar a configuração, verifique se tem os seguintes itens:

- Um dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.

- Um endereço IP público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
    
- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Valores de configuração de exemplo para este exercício


Quando utiliza estes passos como um exercício, pode utilizar os valores de configuração de exemplo:

- Nome da VNet: TestVNet1
- Espaço de Endereços: 10.11.0.0/16 e 10.12.0.0/16
- Sub-redes: 
    - Front-End: 10.11.0.0/24
    - Back-End: 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- Grupo de Recursos: TestRG1
- Localização: EUA Leste
- Servidor DNS: 8.8.8.8
- Nome do Gateway: VNet1GW
- IP Público: VNet1GWIP
- Tipo de VPN: baseada na rota
- Tipo de Ligação: site a site (IPsec)
- Tipo de Gateway: VPN
- Nome do Gateway de Rede Local: Site2
- Nome da Ligação: VNet1toSite2



## 1. Criar uma rede virtual 

Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. Se tiver sub-redes sobrepostas, a ligação não funcionará corretamente. Se a VNet estiver configurada com as definições corretas, pode iniciar os passos da secção [Especificar um servidor DNS](#dns).

### Para criar uma rede virtual

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## 2. Adicionar sub-redes e espaços de endereços adicionais

Pode adicionar sub-redes e espaços de endereços adicionais à VNet depois de ter sido criada.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. Especificar um servidor DNS

Se estiver a criar esta configuração como um exercício, veja estes [valores](#values) ao especificar o servidor DNS.

### Para especificar um servidor DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4. Criar uma sub-rede do gateway

Antes de ligar a rede virtual a um gateway, primeiro tem de criar a sub-rede do gateway para a rede virtual à qual pretende ligar. A sub-rede do gateway que cria tem de ter o nome *GatewaySubnet*. Caso contrário, não funcionará corretamente. 

Para algumas configurações, o prefixo de sub-rede do gateway precisa de uma sub-rede /28 ou superior para acomodar o número de endereços IP necessários no conjunto. Tal significa que o prefixo de sub-rede do gateway tem de ser /28, /27, /26 e etc. Poderá pretender criar uma sub-rede maior aqui para acomodar possíveis adições de configuração futuras.

Se estiver a criar esta configuração como um exercício, veja estes [valores](#values) ao criar a sub-rede do gateway.

### Para criar uma sub-rede do gateway

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5. Criar um gateway de rede virtual

Se estiver a criar esta configuração como um exercício, veja estes [valores](#values) ao criar o gateway.

### Para criar um gateway de rede virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6. Criar um gateway de rede local

O *gateway de rede local* refere-se à sua localização no local. Atribua um nome ao gateway de rede local para o Azure o identificar. 

Se estiver a criar esta configuração como um exercício, veja estes [valores](#values) ao adicionar o site local.

### Para criar um gateway de rede local

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7. Configurar o dispositivo VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8. Criar uma ligação de Rede de VPNs

Crie a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN. Não se esqueça de substituir os valores pelos seus. A chave partilhada tem de corresponder ao valor utilizado na configuração do dispositivo VPN. 

Antes de iniciar esta secção, verifique se foi concluída a criação do gateway de rede virtual e dos gateways de rede local. Se estiver a criar esta configuração como um exercício, veja estes [valores](#values) ao criar a ligação.

### Para criar a ligação VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9. Verificar a ligação VPN

Pode verificar a ligação VPN no portal ou com o PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Passos seguintes

- Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja o [percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) das máquinas virtuais para obter mais informações.

- Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).



<!--HONumber=ago16_HO4-->


