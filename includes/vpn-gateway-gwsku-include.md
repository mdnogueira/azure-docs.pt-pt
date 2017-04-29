Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Quando seleciona um SKU de gateway superior, são alocadas mais CPUs e largura de banda ao gateway e, como resultado, este consegue suportar um débito de rede mais alto para a rede virtual.

O Gateway de VPN pode utilizar os SKUs seguintes:

* Básica
* Standard
* HighPerformance

O Gateway de VPN não utiliza o SKU do gateway de UltraPerformance. Para obter informações sobre o SKU de UltraPerformance, consulte a documentação do [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Quando selecionar um SKU, considere o seguinte:

* Se pretender utilizar um tipo de VPN PolicyBased, tem de utilizar o SKU Básico. As VPNs PolicyBased (anteriormente designadas Encaminhamento Estático) não são suportadas em mais nenhum SKU.
* O BGP não é suportado no SKU Básico.
* As configurações coexistentes do Gateway de VPN do ExpressRoute não são suportadas no SKU Básico.
* As ligações do Gateway de VPN S2S do modo ativo/ativo podem ser configuradas apenas no SKU HighPerformance.

