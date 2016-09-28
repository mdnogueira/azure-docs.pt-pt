- As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.

- Um serviço em nuvem ou um ponto final de balanceamento de carga NÃO PODE abranger várias redes virtuais, mesmo se estiverem ligadas em conjunto.

- A ligação de várias redes virtuais do Azure em conjunto não precisa de gateways de VPN no local, a menos que precise de conetividade em vários locais.

- A VNet a VNet suporta a ligação de redes virtuais. Não suporta a ligação de máquinas virtuais ou serviços em nuvem se NÃO estiverem numa rede virtual.

- A ligação VNet a VNet precisa de gateways de VPN do Azure com tipos de VPN RouteBased (anteriormente denominado Encaminhamento Dinâmico). 

- A conetividade de rede virtual pode ser utilizada em simultâneo com VPNs multilocal, com um máximo de 10 (Gateways Predefinidos/Standard) ou 30 (Gateways de Elevado Desempenho) túneis VPN para um gateway de VPN de rede virtual em ligação a qualquer outra rede virtual ou site no local.

- Os espaços de endereços das redes virtuais e sites de rede local no local não se podem sobrepor. A sobreposição de espaços de endereços fará com que a criação de ligações VNet a VNet falhe.

- Os túneis redundantes entre um par de redes virtuais não são suportados.

- Todos os túneis VPN da rede virtual partilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA do tempo de atividade do gateway de VPN no Azure.

- O tráfego VNet a VNet circula na Microsoft Network e não na Internet.

- O tráfego VNet a VNet na mesma região é gratuito em ambas as direções; o tráfego de saída VNet a VNet entre várias regiões é cobrado com as taxas de transferência de dados inter-VNet de saída baseadas nas regiões de origem. Veja a [página de preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) para obter detalhes.

<!--HONumber=Sep16_HO3-->


