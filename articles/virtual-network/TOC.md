# Descrição geral
## [Redes virtuais](virtual-networks-overview.md)
## [Grupos de segurança de rede](virtual-networks-nsg.md)
## [Rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md)
## Endereçamento IP
### [Resource Manager](virtual-network-ip-addresses-overview-arm.md)
### [Clássico](virtual-network-ip-addresses-overview-classic.md)
## [Peering de rede virtual](virtual-network-peering-overview.md)
## Máquinas virtuais
### [Interfaces de rede](virtual-network-network-interface-overview.md)
### [Resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md)
## [Continuidade do negócio](virtual-network-disaster-recovery-guidance.md)
## [Preços](https://azure.microsoft.com/pricing/details/virtual-network)

# Introdução
## [Criar uma rede virtual](virtual-networks-create-vnet-arm-pportal.md)
## [Implementar uma VM numa rede virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

# Procedimento
## Planear e conceber
### [Redes virtuais](virtual-network-vnet-plan-design-arm.md)
### [Grupos de segurança de rede](virtual-networks-nsg.md)

## Implementação
### Redes virtuais
#### [Portal](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [Modelo](virtual-networks-create-vnet-arm-template-click.md)
#### [Portal (Clássico)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (Clássico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [CLI (Clássica)](virtual-networks-create-vnet-classic-cli.md)

### Grupos de segurança de rede
#### [Portal](virtual-networks-create-nsg-arm-portal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [Modelo](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (Clássico)](virtual-networks-create-nsg-classic-ps.md)
#### [CLI (Clássica)](virtual-networks-create-nsg-classic-cli.md)

### Rotas definidas pelo utilizador
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [Modelo](virtual-network-create-udr-arm-template.md)
#### [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
#### [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)

### Peering de rede virtual
#### [Portal](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [Modelo](virtual-networks-create-vnetpeering-arm-template-click.md)

### Máquinas virtuais

#### Endereços IP públicos estáticos
##### [Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Modelo](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

#### Endereços IP privados estáticos
##### [Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### [Portal (Clássico)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (Clássico)](virtual-networks-static-private-ip-classic-ps.md)
##### [CLI (Clássica)](virtual-networks-static-private-ip-classic-cli.md)

#### Várias interfaces de rede
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [CLI](virtual-network-deploy-multinic-arm-cli.md)
##### [Modelo](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell (Clássico)](virtual-network-deploy-multinic-classic-ps.md)
##### [CLI (Clássica)](virtual-network-deploy-multinic-classic-cli.md)

#### [Vários endereços IP](virtual-network-multiple-ip-addresses-powershell.md)

### Cenários de conectividade
#### [Rede virtual (VNet) para VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet (Resource Manager) para uma VNet (Clássica)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet para rede no local (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet para rede no local (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Arquitetura de rede híbrida de elevada disponibilidade](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Cenários de segurança
#### [Proteger redes com aplicações virtuais](virtual-network-scenario-udr-gw-nva.md)
#### [DMZ entre o Azure e a Internet](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Serviço em nuvem e segurança de rede](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [DMZ simples com NSGs](virtual-networks-dmz-nsg-asm.md)
##### [DMZ com firewall e NSGs](virtual-networks-dmz-nsg-fw-asm.md)
##### [DMZ com firewall, UDR e NSGs](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [Aplicação de exemplo](virtual-networks-sample-app.md)

## Gerir
### Grupos de segurança de rede
#### [Portal](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [Registos](virtual-network-nsg-manage-log.md)
#### Resolução de problemas
##### [Portal](virtual-network-nsg-troubleshoot-portal.md)
##### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Resolver problemas das rotas
#### [Portal](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### Máquinas virtuais
#### [Ver e modificar nomes de anfitriões](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Mover uma VM para outra sub-rede](virtual-networks-move-vm-role-to-subnet.md)

# Referência
## [Cmdlets do PowerShell (Resource Manager)](https://msdn.microsoft.com/library/mt163510(v=azure.300))
## [Cmdlets do PowerShell (Clássico)](https://msdn.microsoft.com/library/mt270335(v=azure.300))
## [APIs (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [APIs (Clássica)](https://msdn.microsoft.com/library/jj157182.aspx)
## [Blogue das redes](http://azure.microsoft.com/blog/topics/networking)
## [Perguntas mais frequentes](virtual-networks-faq.md)

# Relacionado
## [Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines)
## [Gateway de Aplicação](https://azure.microsoft.com/documentation/services/application-gateway)
## [DNS do Azure](https://azure.microsoft.com/documentation/services/dns)
## [Gestor de Tráfego](https://azure.microsoft.com/documentation/services/traffic-manager)
## [Balanceador de Carga](https://azure.microsoft.com/documentation/services/load-balancer)
## [Gateway de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway)
## [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute)



<!--HONumber=Nov16_HO2-->


