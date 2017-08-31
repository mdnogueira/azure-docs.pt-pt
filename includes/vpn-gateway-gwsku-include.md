Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Selecione os SKU que atendem as suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, funcionalidades e SLA.

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>Produção *vs.* Cargas de trabalho Dev-Test

Devido a diferenças nos SLA e nos conjuntos de funcionalidades, é recomendável seguir os SKU para produção *versus* dev-test:

| **Carga de trabalho**                       | **SKU**               |
| ---                                | ---                    |
| **Produção, cargas de trabalho críticas** | VpnGw1, VpnGw2, VpnGw3 |
| **Dev-test ou prova de conceito**   | Básica                  |
|                                    |                        |

Se estiver a usar os SKU antigos, as recomendações de SKU de produção são SKU Standard e de alto desempenho. Para obter informações sobre os SKU antigos, veja [Gateway SKUs (legacy SKUs)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md) (SKU de Gateway (SKUs antigos)).

###  <a name="feature"></a>Conjuntos de funcionalidades do SKU de gateway

Os novos SKU de gateway simplificam os conjuntos de funcionalidades oferecidos em gateways:

| **SKU**| **Funcionalidades**|
| ---    | ---         |
|**Básica**   | **VPN baseada em rota**: 10 túneis com P2S<br><br>**VPN baseada em políticas**: (IKEv1): 1 túnel; nenhum P2S|
| **VpnGw1, VpnGw2, and VpnGw3** | **VPN baseada em rota**: até 30 túneis (*),P2S, BGP, ativo-ativo, política de IPsec/IKE personalizada, coexistência VPN/ExpressRoute |
|        |             |

(*) Pode configurar "PolicyBasedTrafficSelectors" para se ligar a um gateway de VPN baseada em rota (VpnGw1, VpnGw2, VpnGw3) para vários dispositivos com firewall baseada na política no local. Consulte [Gateways de ligação VPN para vários dispositivos VPN com base na política local com o PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter detalhes.

###  <a name="resize"></a>Redimensionar dos SKU de gateway

1. Pode redimensionar entre SKU VpnGw1, VpnGw2 e VpnGw3.
2. Ao trabalhar com os antigos SKU de gateway, pode redimensionar entre SKU Básicos, Standard e de alto desempenho.
2. **Não é possível** redimensionar SKU Básicos/Standard/alto desempenho para SKU VpnGw2/VpnGw1/VpnGw3 novos. Em vez disso, deve [migrar](#migrate) para os novos SKU.

###  <a name="migrate"></a>Migrar de SKU antigos para SKU novos

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
