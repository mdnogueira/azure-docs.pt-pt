> [!NOTE]
> * O endereço IP Público do gateway de VPN será alterado ao migrar de um SKU antigo para um novo SKU.
> * Não pode migrar gateways de VPN clássicos para os SKUs novos. Os gateways de VPN clássicos só podem utilizar os SKUs (antigos) legados.
> 

Não pode redimensionar os seus gateways de VPN do Azure entre os SKU antigos e as novas famílias de SKU. Se tiver gateways VPN no modelo de implementação do gestor de recursos que está a usar a versão mais antiga de SKU, pode migrar para os novos SKU. Para migrar, elimine o gateway de VPN existente na sua rede virtual e crie um novo.

Fluxo de trabalho de migração:

1. Remova várias ligações a um gateway de rede virtual.
2. Elimine o gateway de VPN antigo.
3. Crie o gateway de VPN novo.
4. Atualize os seus dispositivos VPN no local com o novo endereço IP do gateway do VPN (para ligações de rede).
5. Atualize o valor do endereço IP do gateway para gateways de rede local VNet para VNet que se ligam a este gateway.
6. Transfira novos pacotes de configuração do cliente VPN para clientes de P2S que se ligam à rede virtual por meio deste gateway VPN.
7. Volte a criar as ligações a um gateway de rede virtual.