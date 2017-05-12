# Descrição geral
## [Redes virtuais](virtual-networks-overview.md)
## [Rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md)
## [Peering de rede virtual](virtual-network-peering-overview.md)
## [Continuidade do negócio](virtual-network-disaster-recovery-guidance.md)
## [FAQ](virtual-networks-faq.md)
## Endereçamento IP
### [Resource Manager](virtual-network-ip-addresses-overview-arm.md)
### [Clássico](virtual-network-ip-addresses-overview-classic.md)

# Introdução
## [Crie a sua primeira rede virtual](virtual-network-get-started-vnet-subnet.md)

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
#### [Portal](virtual-networks-create-nsg-arm-pportal.md)
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

### Interfaces de rede
#### [Adicionar, alterar ou eliminar](virtual-network-network-interface.md)
#### [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md)

### [Endereços IP públicos](virtual-network-public-ip-address.md)

### Máquinas virtuais
#### [Adicionar ou remover interfaces de rede](virtual-network-network-interface-vm.md) 
#### Criar uma VM com um endereço IP público estático
##### [Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Modelo](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

#### Criar uma VM com um endereço IP privado estático
##### [Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### [Portal (Clássico)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (Clássico)](virtual-networks-static-private-ip-classic-ps.md)
##### [CLI (Clássica)](virtual-networks-static-private-ip-classic-cli.md)

#### Criar uma VM com várias interfaces de rede
##### [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [PowerShell (Clássico)](virtual-network-deploy-multinic-classic-ps.md)
##### [CLI (Clássica)](virtual-network-deploy-multinic-classic-cli.md)

#### Criar uma VM com vários endereços IP
##### [Portal do Azure](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [Modelo](virtual-network-multiple-ip-addresses-template.md)

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

## Configurar
### Redes aceleradas para VMs
#### [Portal do Azure](virtual-network-accelerated-networking-portal.md)
#### [PowerShell](virtual-network-accelerated-networking-powershell.md)
### [Otimizar o débito da rede VM](virtual-network-optimize-network-bandwidth.md)
### Lista de controlo de acesso
#### [Portal clássico](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)
### [Resolução de nomes para VMs e serviços cloud](virtual-networks-name-resolution-for-vms-and-role-instances.md)

## Gerir
### Grupos de segurança de rede
#### [Portal](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [Registos](virtual-network-nsg-manage-log.md)
### Máquinas virtuais
#### [Ver e modificar nomes de anfitriões](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Mover uma VM para outra sub-rede](virtual-networks-move-vm-role-to-subnet.md)

## Resolução de problemas
### Grupos de segurança de rede
#### [Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Rotas
#### [Portal](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Teste de débito](virtual-network-bandwidth-testing.md)

# Referência
## [PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [PowerShell (Clássico)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [CLI do Azure](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (Clássico)](https://msdn.microsoft.com/library/jj157182.aspx)


# Relacionado
## [Máquinas Virtuais](/azure/virtual-machines/)
## [Gateway de Aplicação](/azure/application-gateway/)
## [DNS do Azure](/azure/dns/)
## [Gestor de Tráfego](/azure/traffic-manager/)
## [Balanceador de Carga](/azure/load-balancer/)
## [Gateway de VPN](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Recursos
## [Blogue das redes](http://azure.microsoft.com/blog/topics/networking)
## [Fórum de redes](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Preços](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
