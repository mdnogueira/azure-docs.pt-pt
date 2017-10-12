### <a name="supportedclientos"></a>Que sistemas operativos cliente posso utilizar com a ligação Ponto a Site?

São suportados os seguintes sistemas operativos cliente:

* Windows 7 (32 e 64 bits)
* Windows Server 2008 R2 (apenas 64 bits)
* Windows 8 (32 e 64 bits)
* Windows 8.1 (32 e 64 bits)
* Windows Server 2012 (apenas 64 bits)
* Windows Server 2012 R2 (apenas 64 bits)
* Windows 10
* Versão OSX 10.11 para Mac (El Capitan)
* Versão macOS 10.12 para Mac (Sierra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quantos pontos finais de cliente VPN posso ter na minha configuração Ponto a Site?

Suportamos até 128 clientes VPN para conseguir ligar a uma rede virtual ao mesmo tempo.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Posso atravessar proxies e firewalls com a capacidade Ponto a Site?

O Azure suporta dois tipos de opções de VPN Ponto a site:

* Secure Socket Tunneling Protocol (SSTP). SSTP é uma solução baseada em SSL proprietária da Microsoft que consegue penetrar firewalls, uma vez que a maioria das firewalls abre a porta TCP que o SSL 443 utiliza.

* VPN IKEv2. VPN IKEv2 é uma solução VPN IPsec baseada em normas que utiliza a porta UDP 500 e 4500 e o protocolo IP número 50. As firewalls nem sempre abrem estas portas, pelo que existe a possibilidade de a VPN IKEv2 não conseguir atravessar proxies e firewalls.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se reiniciar o computador cliente configurado para Ponto a Site, a VPN restabelece ligação automaticamente?

Por predefinição, o computador cliente não restabelece a ligação VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>A ligação Ponto a Site suporta o restabelecimento de ligação automático e DDNS nos clientes VPN?

Atualmente, o restabelecimento de ligação automático e DDNS não são suportados nas VPNs Ponto a Site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Posso ter configurações Site a Site e Ponto a Site coexistentes na mesma rede virtual?

Sim. Para o modelo de implementação do Resource Manager, tem de ter um tipo de VPN RouteBased para o seu gateway. No modelo de implementação clássica, precisa de um gateway dinâmico. Não suportamos a ligação Ponto a Site para gateways de VPN de encaminhamento estático ou gateways de VPN PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente Ponto a Site para ligar a várias redes virtuais ao mesmo tempo?

Não. Um cliente Ponto a Site só pode ligar a recursos na VNet em que resida o gateway de rede virtual.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Que débito posso esperar através das ligações Site a Site ou Ponto a Site?

É difícil manter o débito exato dos túneis VPN. O IPsec e SSTP são protocolos VPN extremamente encriptados. O débito também é limitado pela latência e pela largura de banda entre o local e a Internet. Para um Gateway de VPN com apenas ligações VPN Ponto a Site IKEv2, o débito total que pode esperar depende do SKU de Gateway. Para obter mais informações sobre o débito, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Posso utilizar qualquer cliente VPN de software para a ligação Ponto a Site que suporte SSTP e/ou IKEv2?

Não. Só pode utilizar o cliente VPN nativo no Windows para SSTP e o cliente VPN nativo no Mac para IKEv2. Veja a lista de sistemas operativos cliente suportados.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>O Azure suporta a VPN IKEv2 no Windows?

Os utilizadores podem ligar-se ao Azure através do cliente VPN do Windows incorporado que suporta IKEv2. No entanto, as ligações IKEv2 a partir de um dispositivo Windows não funcionam no seguinte cenário:

  Quando o dispositivo do utilizador contém um grande número de certificados de raiz fidedigna, o tamanho de payload da mensagem durante a troca IKE é grande e provoca a fragmentação da camada do IP. Os fragmentos são rejeitados no lado do Azure, o que resulta na falha da ligação. O número de certificados exato em que este problema ocorre é difícil de estimar. Consequentemente, não é garantido o funcionamento das ligações IKEv2 a partir de dispositivos Windows. Quando configura o SSTP e o IKEv2 num ambiente misto (composto por dispositivos Windows e Mac), o perfil da VPN do Windows tenta sempre o túnel IKEv2 primeiro. Se falhar devido ao problema aqui descrito, retrocede para SSTP.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Que outras plataformas não Windows e Mac suporta o Azure na VPN P2S?

O Azure suporta apenas Windows e Mac na VPN P2S.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enabled-radius-andor-ikev2-vpn-on-it"></a>Já tenho um Gateway de VPN do Azure implementado. Posso ativar a VPN RADIUS e/ou IKEv2 no mesmo?

Sim, pode ativar estas novas funcionalidades no gateway já implementado, através da Powershell e do portal do Azure.
