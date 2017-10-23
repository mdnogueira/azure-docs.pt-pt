# Descrição geral
## [Acerca do Gateway de VPN](vpn-gateway-about-vpngateways.md)
## [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md)
## [Subscrição e limites do serviço](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Introdução
## [Planear e conceber para o Gateway de VPN](vpn-gateway-plan-design.md)
## [Acerca das definições do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md)
## [Acerca de dispositivos VPN](vpn-gateway-about-vpn-devices.md)
## [Sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md)
## [Acerca do Gateway de VPN e BGP](vpn-gateway-bgp-overview.md)
## [Acerca da conectividade de elevada disponibilidade](vpn-gateway-highlyavailable.md)
## [Acerca das ligações Ponto a Site](point-to-site-about.md)

# Procedimento
## Configurar ligações Site a Site
### [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [CLI do Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)

## Configurar ligações Ponto a Site - autenticação de certificados nativa do Azure
### Configurar uma VPN P2S
#### [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Gerar certificados autoassinados
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Criar e instalar os ficheiros de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md)
### [Instalar certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Configurar ligações Ponto a Site - autenticação RADIUS
### Configurar uma VPN P2S
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Criar e instalar os ficheiros de configuração de cliente VPN](point-to-site-vpn-client-configuration-radius.md)

## Configurar ligações VNet a VNet
### [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
### [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Configurar uma ligação VNet a VNet entre modelos de implementação
### [Portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Configurar ligações coexistentes de site a site e do ExpressRoute
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Configurar várias ligações Site a Site
### [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (clássico)](vpn-gateway-multi-site.md)
## Ligar vários dispositivos VPN baseados em políticas
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Configurar políticas de IPsec/IKE em ligações
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Configurar ligações ativas/ativas de elevada disponibilidade
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Configurar o BGP para um gateway de VPN
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [CLI do Azure](bgp-how-to-cli.md)
## Configurar túnel forçado
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (clássico)](vpn-gateway-about-forced-tunneling.md)
## Modificar as definições do gateway de rede local
### [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Verificar uma ligação do gateway de VPN](vpn-gateway-verify-connection-resource-manager.md)
## [Repor um gateway de VPN](vpn-gateway-resetgw-classic.md)
## Eliminar um gateway de VPN
### [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Configurar um gateway de VPN (clássico)](vpn-gateway-configure-vpn-gateway-mp.md)
## [SKUs de gateway (legado)](vpn-gateway-about-skus-legacy.md)
## Configurar dispositivos VPN de terceiros
### [Descrição geral e configuração do Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Exemplo: dispositivo Cisco ASA (IKEv2/não-BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## Resolução de problemas
### [Validar o débito de VPN para uma VNet](vpn-gateway-validate-throughput-to-vnet.md)
### [Definições do dispositivo VPN ou de firewall sugeridas pela comunidade](vpn-gateway-third-party-settings.md)
### [Problema de ligação Ponto a Site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [A ligação Site a Site desliga-se intermitentemente](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [Não é possível estabelecer a ligação Site a Site](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 
### [Configurar e validar ligações VNet ou VPN](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# Referência
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (clássico)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (clássico)](https://msdn.microsoft.com/library/jj154113)
## [CLI do Azure](/cli/azure/network/vnet-gateway)

# Relacionado
## [Rede Virtual](/azure/virtual-network/)
## [Gateway de Aplicação](/azure/application-gateway/)
## [DNS do Azure](/azure/dns/)
## [Gestor de Tráfego](/azure/traffic-manager/)
## [Balanceador de Carga](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Blogue](https://azure.microsoft.com/blog/topics/networking)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
