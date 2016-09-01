<properties
   pageTitle="Encaminhamento Assimétrico | Microsoft Azure"
   description="Este artigo aborda problemas com os quais um cliente se pode deparar, relacionados com o encaminhamento assimétrico na sua rede, quando existem várias ligações para um destino."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"/>

# Encaminhamento Assimétrico com vários caminhos de rede

Este artigo explica como o tráfego de encaminhamento e retorno pode seguir rotas diferentes quando existem vários caminhos disponíveis entre a origem e o destino.

Para compreender o encaminhamento assimétrico, é necessário compreender dois conceitos. Um é o impacto de vários caminhos de rede. O segundo é o comportamento dos dispositivos que mantêm o estado, tais como as firewalls. Estes dispositivos denominam-se dispositivos com monitorização de estado. Uma combinação destes dois fatores cria cenários em que o tráfego é ignorado por um dispositivo com monitorização de estado por este não ter visto o tráfego originado através dele.

## Vários Caminhos de Rede

Quando uma rede empresarial tem apenas uma ligação à Internet através do respetivo fornecedor de serviços Internet, todo o tráfego de e para a Internet passa pelo mesmo caminho. Muitas vezes, as empresas adquirem vários circuitos, como caminhos redundantes, para melhorar o tempo de atividade da rede. Nestes casos, é possível que o tráfego que sai para fora da rede para a Internet passe por uma ligação enquanto o tráfego de retorno chega por outra ligação. Este fenómeno é normalmente conhecido como Encaminhamento Assimétrico, em que o tráfego inverso segue um caminho diferente do fluxo original.

![Encaminhamento 3](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Embora a descrição anterior diga respeito à Internet, aplica-se a outras combinações de vários caminhos. Os exemplos incluem um caminho da Internet e um caminho privado para o mesmo destino, vários caminhos privados para o mesmo destino, etc. 

Da origem até ao destino, cada router calcula o melhor caminho para chegar ao destino. A determinação do melhor caminho possível baseia-se em dois fatores principais.

1.  O encaminhamento entre redes externas baseia-se no protocolo de encaminhamento Border Gateway Protocol, normalmente designado de BGP. O BGP pega nos anúncios vizinhos e sujeita-os a vários passos para determinar o melhor caminho para o destino, que instala depois na respetiva tabela de encaminhamento.
2.  Comprimento da máscara de sub-rede associado a uma rota. Se forem recebidos vários anúncios para o mesmo endereço IP, mas com máscaras de sub-rede diferentes, o anúncio com a máscara de sub-rede maior é preferido porque é considerado a rota mais específica.

## Dispositivos com Monitorização de Estado

Os routers procuram o cabeçalho IP do pacote para fins de encaminhamento. No entanto, há dispositivos que fazem uma pesquisa ainda mais exaustiva no interior do pacote. Normalmente, estes dispositivos procuram cabeçalhos Layer4 (TCP/UDP) ou até mesmo Layer7 (Camada de Aplicação). Estes dispositivos são dispositivos de segurança ou dispositivos de otimização de largura de banda. A firewall é um exemplo comum de dispositivos com monitorização de estado. A firewall permite ou nega um pacote através das respetivas interfaces com base em vários campos, como o protocolo, a porta TCP/UDP ou os cabeçalhos de URL. Esta inspeção de pacotes coloca muita carga de processamento no dispositivo. Para melhorar o desempenho, a firewall inspeciona o primeiro pacote de um fluxo. Se o pacote for permitido, mantém as informações de fluxo na respetiva tabela de estados. Todos os pacotes subsequentes relacionados com este fluxo são permitidos com base na decisão inicial. Por conseguinte, quando um pacote de um fluxo existente chega à firewall e esta não dispõe de informações de estado anteriores relativas ao mesmo, ignora este pacote.

## Encaminhamento assimétrico com o ExpressRoute

Quando se liga à Microsoft, através do ExpressRoute, ocorrem as seguintes alterações na sua rede.

1.  Tem várias ligações à Microsoft. Uma ligação é a ligação à Internet existente e a outra é através do ExpressRoute. Algum tráfego para a Microsoft pode seguir através da Internet mas voltar através do ExpressRoute ou vice-versa.
2.  Pode receber mais endereços IP específicos através do ExpressRoute. Portanto, o tráfego da sua rede para a Microsoft relativo a serviços oferecidos através do ExpressRoute prefere sempre o ExpressRoute. 

Para compreender o impacto dos dois casos acima, vejamos alguns cenários. Suponhamos que tem apenas um circuito para Internet e que consome todos os serviços da Microsoft através da Internet. O tráfego da rede para a Microsoft e vice-versa atravessa a mesma ligação da Internet e passa através da firewall. A firewall regista o fluxo quando vê o primeiro pacote, sendo os pacotes de retorno permitidos quando existe fluxo na tabela de estados.

![Encaminhamento 1](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


Agora pode ativar ExpressRoute e consumir os serviços oferecidos pela Microsoft através do ExpressRoute. Todos os outros serviços da Microsoft são consumidos através da Internet. Implemente uma firewall separada de extremidade com ligação ao ExpressRoute. A Microsoft vai anunciar prefixos mais específicos para a sua rede através do ExpressRoute para serviços específicos. A infraestrutura de encaminhamento irá escolher o ExpressRoute como o caminho preferido para esses prefixos. Se não estiver a anunciar os seus endereços IP públicos para a Microsoft através do ExpressRoute, a Microsoft irá comunicar com os endereços IP públicos através da Internet. Portanto, o encaminhamento de tráfego da rede para a Microsoft irá utilizar o ExpressRoute enquanto que o tráfego inverso da Microsoft irá utilizar a Internet. Quando a firewall de extremidade vê um pacote de resposta de um fluxo não encontrado na tabela de estados, ignora o tráfego de retorno. 

Se optar por utilizar o mesmo conjunto NAT para o ExpressRoute e para a Internet, irá ver problemas semelhantes com os clientes nos endereços IP privados na sua rede. O pedido de serviços como o Windows Update será enviado através da Internet, uma vez que os endereços IP destes serviços não são anunciados através do ExpressRoute. No entanto, o tráfego de retorno irá chegar através do ExpressRoute. Se a Microsoft receber um endereço IP com a mesma máscara de sub-rede da Internet e do ExpressRoute, prefere o ExpressRoute à Internet. Se uma firewall ou outro dispositivo com monitorização de estado na extremidade da rede, voltada para o ExpressRoute, não tiver informações sobre o fluxo, irá ignorar os pacotes que pertencem a esse fluxo. 

## Soluções para Encaminhamento Assimétrico

Há duas formas principais de resolver o problema do Encaminhamento Assimétrico. Uma é através de encaminhamento e a outra através de NAT baseado na origem (SNAT). 

1. Encaminhamento 

    - É necessário assegurar que os seus endereços IP públicos são anunciados para ligações WAN adequadas. Por exemplo, se pretender utilizar a Internet para o tráfego de autenticação e o ExpressRoute para o tráfego de correio. Nesse caso, não pode anunciar os endereços IP públicos ADFS através do ExpressRoute. Analogamente, o servidor ADFS no local não pode ser exposto aos endereços IP recebidos através do ExpressRoute. As rotas recebidas através do ExpressRoute são mais específicas, o que torna o ExpressRoute o caminho preferido para o tráfego de autenticação para a Microsoft e, por conseguinte, causam encaminhamento assimétrico.

    - Se quiser utilizar o ExpressRoute para autenticação, tem de certificar-se de que anuncia os endereços IP públicos ADFS através do ExpressRoute sem NAT. Deste modo, o tráfego proveniente da Microsoft para um servidor ADFS no local passa por cima do ExpressRoute, enquanto que o tráfego de retorno a partir do cliente para a Microsoft utiliza o ExpressRoute por ser preferencial à Internet. 

2. NAT baseado na origem

    Outra maneira de resolver problemas de Encaminhamento Assimétrico é através do NAT de origem (SNAT). Por exemplo, se não tiver anunciado o endereço IP público do servidor SMTP no local através do ExpressRoute com a intenção de utilizar a Internet para esta comunicação. Um pedido proveniente da Microsoft para o seu servidor SMTP no local atravessa a Internet. Fornece ao NAT o pedido de entrada para um endereço IP interno. O tráfego inverso do servidor SMTP é enviado para a firewall de extremidade (utilizada para efeitos de NAT) em vez do ExpressRoute. Deste modo, o tráfego de retorno irá voltar através da Internet. 


![Encaminhamento 2](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## Deteção de Encaminhamento Assimétrico

O Traceroute é a melhor forma de garantir que o tráfego atravessa o caminho esperado. Se pretender que o tráfego do seu servidor SMTP no local para a Microsoft assuma o caminho da Internet, faça o traceroute do servidor SMTP para o Office 365. O resultado confirma se o tráfego está ou não a sair da rede para a Internet e não para o ExpressRoute. 





<!--HONumber=ago16_HO4-->


