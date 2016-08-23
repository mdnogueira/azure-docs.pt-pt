|    | **Débito do VPN Gateway (1)** | **Máximo de túneis IPsec do VPN Gateway (2)** | **Débito do Gateway do ExpressRoute** | **Coexistência do ExpressRoute e do VPN Gateway**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU Básico**              |  100 Mbps | 10                         |  500 Mbps                           | Não   |
| **SKU Standard**           |  100 Mbps | 10                         | 1000 Mbps                           | Sim  |
| **SKU de Elevado Desempenho (3)**   | 200 Mbps  | 30                         | 2000 Mbps                           | Sim  |

- (1) O débito da VPN é uma estimativa aproximada baseada em medidas entre VNets na mesma região do Azure. Não é uma garantia do que pode receber para ligações em vários locais através da Internet, mas deve ser utilizado como uma medida máxima possível.
- (2) O número de túneis refere-se à VPN baseada na rota abaixo. Uma VPN baseada em políticas só pode suportar um túnel VPN de Rede de VPNs.
- (3) A SKU de Elevado Desempenho não é suportada para o tipo de VPN baseada em políticas.


<!--HONumber=Aug16_HO1-->


