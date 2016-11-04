### O BGP é suportado em todos os SKUs do VPN Gateway do Azure?
Não, o BGP é suportado nos gateways de VPN **Standard** e **HighPerformance** do Azure. O SKU **Básico** NÃO é suportado.

### Posso utilizar o BGP com gateways de VPN do Azure baseados em políticas?
Não, o BGP é apenas suportado em gateways de VPN baseados na rota.

### Posso utilizar ASNs (Números de Sistema Autónomo) privados?
Sim, pode utilizar os seus próprios ASNs públicos ou privados para as suas redes no local e para redes virtuais do Azure.

#### Existem ASNs reservados pelo Azure?
Sim, os seguintes ASNs são reservados pelo Azure para peerings internos e externos:

* ASNs públicos: 8075, 8076, 12076
* ASNs privados: 65515 65517, 65518, 65519, 65520

Não pode especificar estes ASNs para os seus dispositivos VPN no local quando se liga a gateways de VPN do Azure.

### Posso utilizar o mesmo ASN para as redes VPN no local e as VNets do Azure?
Não, tem de atribuir ASNs diferentes entre as suas redes no local e as VNets do Azure se estiver a ligá-las entre si com o BGP. Os VPN Gateways do Azure têm atribuída uma predefinição ASN de 65515,quer o BGP esteja ou não ativado para conectividade em vários locais. Pode substituir esta predefinição atribuindo um ASN diferente ao criar o gateway de VPN ou alterar o ASN depois do gateway ter sido criado. Terá de atribuir os seus ASNs no local aos Gateways de Rede Local do Azure correspondentes.

### Que prefixos de endereços irão os gateways de VPN do Azure anunciar-me?
O gateway de VPN do Azure irá anunciar as seguintes rotas para os seus dispositivos BGP no local:

* Os seus prefixos de endereços VNet
* Prefixos de endereços para cada Gateway de Rede Local ligado ao gateway de VPN do Azure
* Rotas aprendidas de outras sessões de peering de BGP ligadas ao gateway de VPN do Azure, **exceto a rota ou as rotas predefinidas que se sobreponham a um prefixo VNet**.

#### Posso anunciar a rota predefinida (0.0.0.0/0) para gateways de VPN do Azure?
Neste momento, não.

#### Posso anunciar os prefixos exatos como prefixos da minha Rede Virtual?
Não, o ato de anunciar os mesmos prefixos como prefixos de endereços da sua Rede Virtual será bloqueado ou filtrado pela plataforma do Azure. No entanto, pode anunciar um prefixo que é um conjunto mais amplo do que tem no interior da Rede Virtual. Por exemplo, a Rede Virtual pode utilizar o espaço de endereços 10.10.0.0/16 e pode anunciar 10.0.0.0/8.

### Posso utilizar o BGP com as minhas ligações VNet a VNet?
Sim, pode utilizar o BGP para ligações em vários locais e ligações VNet a VNet.

### Posso combinar ligações BGP com ligações não BGP para os meus gateways de VPN do Azure?
Sim, pode misturar ligações BGP e não BGP para o mesmo gateway de VPN do Azure.

### O gateway de VPN do Azure suporta encaminhamento de trânsito BGP?
Sim, o encaminhamento de trânsito BGP é suportado. No entanto, os gateways de VPN do Azure **NÃO** anunciarão rotas predefinidas a outros elementos de rede BGP. Para ativar o encaminhamento de trânsito através de vários gateways de VPN do Azure, tem de ativar o BGP em todas as ligações VNet para VNet intermédias.

### Posso ter mais do que um túnel entre o gateway de VPN do Azure e a minha rede no local?
Sim, pode estabelecer mais do que um túnel VPN S2S entre um gateway de VPN do Azure e a sua rede no local. Tenha em atenção que todos estes túneis serão contados tendo em conta o número total de túneis dos seus gateways de VPN do Azure. Por exemplo, se tiver dois túneis redundantes entre o gateway de VPN do Azure e um da sua rede no local, irá consumir dois túneis da quota total para o seu gateway de VPN do Azure (10 para Standard e 30 para HighPerformance).

### Posso ter vários túneis entre duas VNets do Azure com BGP?
Não, os túneis redundantes entre um par de redes virtuais não são suportados.

### Posso utilizar o BGP para VPN S2S numa configuração de coexistência ExpressRoute/VPN S2S?
Neste momento, não.

### Que endereço utiliza o gateway de VPN do Azure para o IP do Elemento de Rede BGP?
O gateway de VPN do Azure irá alocar um único endereço IP a partir do intervalo GatewaySubnet definido para a rede virtual. Por predefinição, é o penúltimo endereço do intervalo. Por exemplo, se o seu GatewaySubnet for 10.12.255.0/27, situado entre 10.12.255.0 e 10.12.255.31, o endereço IP do Elemento de Rede BGP no gateway de VPN do Azure será 10.12.255.30. Pode encontrar estas informações quando listar as informações do gateway de VPN do Azure.

### Quais são os requisitos dos endereços IP do Elemento de Rede BGP no meu dispositivo VPN?
O seu endereço do elemento de rede BGP no local **NÃO DEVE** ser o mesmo que o endereço IP público do seu dispositivo VPN. Utilize um endereço IP diferente no dispositivo VPN do IP do Elemento de Rede BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. Especifique este endereço no Gateway de Rede Local correspondente que representa a localização.

### O que devo especificar como prefixos de endereços para o Gateway de Rede Local ao utilizar o BGP?
O Gateway de Rede Local do Azure especifica os prefixos de endereços iniciais da rede no local. Com o BGP, tem de alocar o prefixo de anfitrião (prefixo /32) do seu endereço IP do Elemento de Rede BGP como o espaço de endereços para essa rede no local. Se o IP do Elemento de Rede BGP for 10.52.255.254, deve especificar “10.52.255.254/32” como o localNetworkAddressSpace do Gateway de Rede Local que representa esta rede no local. Trata-se de garantir que o gateway de VPN do Azure estabelece a sessão BGP através do túnel VPN S2S.

### O que devo adicionar ao meu dispositivo VPN no local para a sessão de peering de BGP?
Deve adicionar uma rota de anfitrião do endereço IP do Elemento de Rede BGP do Azure no dispositivo VPN para apontar para o túnel VPN S2S IPsec. Por exemplo, se o IP do Elemento VPN do Azure for “10.12.255.30”, deve adicionar no dispositivo VPN uma rota de anfitrião para “10.12.255.30” com uma interface nexthop da interface de túnel IPsec correspondente.

<!--HONumber=Sep16_HO3-->


