- **Tipo de VPN baseada em políticas:** as VPNs baseadas em políticas eram anteriormente denominadas gateways de encaminhamento estático no modelo de implementação clássica. As VPNs baseadas em políticas encriptam e direcionam os pacotes através de túneis IPsec baseados nas políticas IPsec configuradas com as combinações de prefixos de endereços entre a rede no local e a VNet do Azure. Normalmente, a política (ou o seletor de tráfego), é definido como uma lista de acesso na configuração do dispositivo VPN. O valor para um tipo de VPN baseada em políticas é *PolicyBased*.

- **Tipo de VPN baseada na rota**: as VPNs baseadas na rota eram anteriormente denominadas gateways de encaminhamento dinâmico no modelo de implementação clássica. As VPNs baseadas na rota utilizam “rotas” no reencaminhamento IP ou na tabela de encaminhamento para direcionar os pacotes para as respetivas interfaces de túnel. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política (ou o seletor de tráfego) das VPNs baseadas na rota é configurada como “de qualquer ponto a qualquer ponto” (ou carateres universais). O valor do tipo de VPN baseada na rota é *RouteBased*.


<!--HONumber=Jun16_HO2-->


