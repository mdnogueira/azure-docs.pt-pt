|  | **Débito do VPN Gateway (1)** | **Máximo de túneis IPsec do VPN Gateway (2)** | **Débito do Gateway do ExpressRoute** | **Coexistência do ExpressRoute e do VPN Gateway** |
| --- | --- | --- | --- | --- |
| **SKU Básico** |100 Mbps |10 |500 Mbps |Não |
| **SKU Standard (3)** |100 Mbps |10 |1000 Mbps |Sim |
| **SKU de Elevado Desempenho (3)** |200 Mbps |30 |2000 Mbps |Sim |

* (1) O débito da VPN é uma estimativa aproximada baseada em medidas entre VNets na mesma região do Azure. Não é um débito garantido para ligações entre locais na Internet. É a medida de débito mais alta possível.
* (2) O número de túneis refere-se às VPNs RouteBased. Uma VPN PolicyBased só pode suportar um túnel VPN de Rede de VPNs.
* (3) As VPNs PolicyBased não são suportadas neste SKU. Só são suportadas no SKU Básico.

<!--HONumber=Sep16_HO3-->


