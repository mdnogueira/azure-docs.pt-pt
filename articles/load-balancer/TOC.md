# Descrição geral
## [O que é o Balanceador de Carga?](load-balancer-overview.md)
## [O que é o Balanceador de Carga Standard?](load-balancer-standard-overview.md)
## [Balanceador de Carga de Externo](load-balancer-internet-overview.md)
## [Balanceador de carga interno](load-balancer-internal-overview.md)
## [Compreender as sondas do balanceador de carga](load-balancer-custom-probe-overview.md)
## [Compreender as Portas de Disponibilidade Elevada](load-balancer-ha-ports-overview.md)
## [Suporte para o Azure Resource Manager](load-balancer-arm.md)
## [Suporte para IPv6](load-balancer-ipv6-overview.md)
## [Vários VIPs](load-balancer-multivip-overview.md)
## [Compreender as ligações de saída](load-balancer-outbound-connections.md)

# Introdução

## [Configurar o Balanceador de Carga Interno](load-balancer-get-started-ilb-arm-portal.md)
### [Configurar o Balanceador de Carga Interno (PowerShell)](load-balancer-get-started-ilb-arm-ps.md)
### [Configurar o Balanceador de Carga Interno (CLI)](load-balancer-get-started-ilb-arm-cli.md)
### [Configurar o Balanceador de Carga Interno (Modelo)](load-balancer-get-started-ilb-arm-template.md)

## [Configurar o Balanceador de Carga Interno para Serviços Cloud](load-balancer-get-started-ilb-classic-cloud.md)
### [Configurar o Balanceador de Carga Interno para Serviços Cloud (PowerShell)](load-balancer-get-started-ilb-classic-ps.md)
### [Configurar o Balanceador de Carga Interno para Serviços Cloud (CLI)](load-balancer-get-started-ilb-classic-cli.md)

## [Configurar o Balanceador de Carga com Acesso à Internet](load-balancer-get-started-internet-portal.md)
### [Configurar o Balanceador de Carga com acesso à Internet (PowerShell)](load-balancer-get-started-internet-arm-ps.md)
### [Configurar o Balanceador de Carga com acesso à Internet (CLI)](load-balancer-get-started-internet-arm-cli.md)
### [Configurar o Balanceador de Carga com acesso à Internet (Modelo)](load-balancer-get-started-internet-arm-template.md)

## [Configurar o Balanceador de Carga com acesso à Internet - (PowerShell Clássico)](load-balancer-get-started-internet-classic-ps.md)
### [Configurar o Balanceador de Carga com acesso à Internet (Cloud Clássica)](load-balancer-get-started-internet-classic-cloud.md)
### [Configurar o Balanceador de Carga com acesso à Internet (CLI Clássica)](load-balancer-get-started-internet-classic-cli.md)

## [Criar um balanceador de carga com acesso à Internet com IPv6](load-balancer-ipv6-internet-ps.md)
### [Criar um balanceador de carga com acesso à Internet com IPv6 (CLI)](load-balancer-ipv6-internet-cli.md)
### [Criar um balanceador de carga com acesso à Internet com IPv6 (Modelo)](load-balancer-ipv6-internet-template.md)

## [Criar um Balanceador de Carga Standard Público com redundância de zona](load-balancer-get-started-internet-az-portal.md)
### [Criar um Balanceador de Carga Standard Público com redundância de zona (PowerShell)](load-balancer-get-started-internet-az-powershell.md)
### [Criar um Balanceador de Carga Standard Público com redundância de zona (CLI)](load-balancer-get-started-internet-az-cli.md)

# Procedimento
## [Configurar o tempo limite de inatividade de TCP para o Balanceador de Carga](load-balancer-tcp-idle-timeout.md)
## [Configurar o Modo de distribuição do Balanceador de Carga](load-balancer-distribution-mode.md)
## [Configurar o Balanceador de Carga Interno para o SQL AlwaysOn](load-balancer-configure-sqlao.md)
## [Configurar vários VIPs para o serviço cloud](load-balancer-multivip.md)
## [Combinar serviços de balanceamento de carga](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fload-balancer%2ftoc.json)
## [Utilizar várias configurações de IP](load-balancer-multiple-ip.md)
### [Utilizar várias configurações de IP (CLI)](load-balancer-multiple-ip-cli.md)
### [Utilizar várias configurações de IP (PowerShell)](load-balancer-multiple-ip-powershell.md)
## [Log Analytics para o Balanceador de Carga do Azure](load-balancer-monitor-log.md)
## [Configurar DHCPv6 para VMs do Linux](load-balancer-ipv6-for-linux.md)
## [Configurar Portas de Elevada Disponibilidade para o Balanceador de Carga Interno](load-balancer-configure-ha-ports.md)
## [Criar um endereço IP público numa zona de disponibilidade](../virtual-network/create-public-ip-availability-zone-portal.md)
## [Criar um endereço IP público numa zona de disponibilidade (PowerShell)](../virtual-network/create-public-ip-availability-zone-powershell.md)
## [Criar um endereço IP público numa zona de disponibilidade (CLI)](../virtual-network/create-public-ip-availability-zone-cli.md)
## Resolução de problemas
### [Resolver problemas do Balanceador de Carga do Azure](load-balancer-troubleshoot.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=load-balancer)
## [Azure PowerShell](/powershell/module/azurerm.network)
## [CLI do Azure](/cli/azure/network/lb)
## [.NET](/dotnet/api/microsoft.azure.management.network.models)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-network/latest/LoadBalancers.html)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/ARM/Network/LoadBalancers)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.operations.html#azure.mgmt.network.operations.LoadBalancersOperations)
## [REST](https://msdn.microsoft.com/library/azure/mt163651.aspx)

# Relacionado
## [Gateway de Aplicação](/azure/application-gateway/)
## [Express Route](/azure/expressroute/)
## [Rede Virtual](/azure/virtual-network/)
## [Gateway de VPN](/azure/vpn-gateway/)
## [Máquina Virtual](/azure/virtual-machines/)
## [Gestor de Tráfego](/azure/traffic-manager/)
## [DNS](/azure/dns/)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Preços](https://azure.microsoft.com/pricing/details/load-balancer/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=load-balancer)
