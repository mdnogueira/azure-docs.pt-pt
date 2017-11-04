|  | **Ponto a Site** | **Site a Site** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Serviços suportados pelo Azure** |Cloud Services e Virtual Machines |Cloud Services e Virtual Machines |[Lista de serviços](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Larguras de banda típicas** |Normalmente, < 100 Mbps agregados |Normalmente, < 1 Gbps agregado |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protocolos suportados** |Secure Sockets Tunneling Protocol (SSTP) |IPsec |Ligação direta por VLANs, tecnologias VPN do NSP (MPLS, VPLS,...) |
| **Encaminhamento** |RouteBased (dinâmico) |Suportamos PolicyBased (encaminhamento estático) e RouteBased (VPN de encaminhamento dinâmico) |BGP |
| **Resiliência de ligação** |ativa-passiva |Ativa-passiva ou ativo-ativo |ativa-ativa |
| **Caso de utilização típica** |Criar protótipos, cenários de desenvolvimento/teste/laboratório para serviços em nuvem e máquinas virtuais |Cenários de desenvolvimento/teste/laboratório e cargas de trabalho de produção de pequena escala para serviços em nuvem e máquinas virtuais |Acesso a todos os serviços do Azure (lista validada), Cargas de trabalho de classe empresarial fundamentais, Cópia de segurança, Macrodados, Azure como um site de DR |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Preços** |[Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Preços](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Documentação técnica** |[Documentação do VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentação do VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **FAQ** |[FAQ do Gateway de VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[FAQ do Gateway de VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[FAQ do ExpressRoute](../articles/expressroute/expressroute-faqs.md) |

