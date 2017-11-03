---
title: "Ligação híbrida com a aplicação de camada 2 | Microsoft Docs"
description: "Saiba como implementar aplicações virtuais e UDR para criar um ambiente de aplicação multicamadas no Azure"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: jdial
ms.openlocfilehash: 544ba6484b23da425d53594622122b1e18b92359
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="virtual-appliance-scenario"></a>Cenário de aplicação virtual
Um cenário comum entre o cliente do Azure maior é a necessidade de fornecer uma aplicação de duas camadas exposta à Internet, ao permitir o acesso para a camada de back-a partir de um datacenter no local. Este documento irá guiá-lo através de um cenário de rotas definidas pelo utilizador (UDR), um Gateway de VPN e os dispositivos de rede virtual a utilizar para implementar um ambiente de duas camadas que cumpre os seguintes requisitos:

* Aplicação Web tem de ser acessível a partir apenas da Internet pública.
* Servidor Web que aloja a aplicação tem de ser capaz de aceder a um servidor de aplicação de back-end.
* Todo o tráfego da Internet para a aplicação web tem de passar por uma aplicação virtual de firewall. Esta aplicação virtual será utilizada para apenas o tráfego de Internet.
* Todo o tráfego destinado ao servidor da aplicações tem de passar por uma aplicação virtual de firewall. Esta aplicação virtual será utilizada para acesso ao servidor do fim de back-end e estará disponível da rede no local através de um Gateway de VPN de acesso.
* Os administradores devem ser capazes de gerir as aplicações virtuais de firewall de computadores no local, utilizando uma terceira firewall de aplicação virtual utilizada exclusivamente para efeitos de gestão.

Este é um cenário de rede de Perímetro padrão com uma rede de Perímetro e uma rede protegida. Esse cenário pode ser criado no Azure utilizando NSGs, aplicações virtuais firewall ou uma combinação de ambos. A tabela abaixo mostra algumas dos profissionais de TI e contras entre os NSGs e aplicações virtuais de firewall.

|  | Profissionais de TI | Contras |
| --- | --- | --- |
| NSG |Sem qualquer custo. <br/>Integrado no RBAC do Azure. <br/>As regras podem ser criadas em modelos ARM. |Complexidade pode variar em ambientes de maior. |
| Firewall |Controlo total sobre plane de dados. <br/>Gestão centralizada através da consola da firewall. |Custo de aplicação de firewall. <br/>Não é integrado com o RBAC do Azure. |

A solução abaixo utiliza aplicações virtuais de firewall para implementar um cenário de rede DMZ/protegidos.

## <a name="considerations"></a>Considerações
Pode implementar o ambiente explicado anteriormente no Azure utilizando as diferentes funcionalidades disponíveis hoje em dia, da seguinte forma.

* **Rede virtual (VNet)**. Uma VNet do Azure funciona de forma semelhante a uma rede no local e podem ser segmentada para uma ou mais sub-redes para fornecer o isolamento de tráfego e separação das preocupações.
* **Aplicação virtual**. Vários parceiros de fornecer aplicações virtuais no Azure Marketplace que podem ser utilizadas para as três firewalls descritas acima. 
* **(UDR) de rotas definidas pelo utilizador**. As tabelas de rotas podem conter UDRs utilizadas por redes do Azure para controlar o fluxo de pacotes numa VNet. Estas tabelas de rota podem ser aplicadas a sub-redes. Uma das funcionalidades mais recentes no Azure é a capacidade de aplicar uma tabela de rota para o GatewaySubnet, que fornece a capacidade para encaminhar todo o tráfego proveniente para a VNet do Azure de uma ligação híbrida, para uma aplicação virtual.
* **Reencaminhamento IP**. Por predefinição, o motor de rede do Azure reencaminhar pacotes para placas de interface de rede virtual (NICs) apenas se o endereço IP de destino do pacote corresponde ao endereço IP de NIC. Por conseguinte, se um UDR define que deve ser enviado um pacote para uma determinada aplicação virtual, o motor de rede do Azure seria remover esse pacote. Para garantir que o pacote é entregue para uma VM com (neste caso, uma aplicação virtual) que não seja o destino real para o pacote, tem de ativar o reencaminhamento IP para a aplicação virtual.
* **Grupos de segurança (NSGs) de rede**. O exemplo abaixo não faz a utilizar os NSGs, mas pode utilizar os NSGs aplicados a sub-redes e/ou NICs nesta solução para continuar a filtrar o tráfego que entra e sai nessas NICs e sub-redes.

![Conetividade IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Neste exemplo, não há uma subscrição que contém o seguinte:

* 2 grupos de recursos não mostrados no diagrama. 
  * **ONPREMRG**. Contém todos os recursos necessários para simular uma rede no local.
  * **AZURERG**. Contém todos os recursos necessários para o ambiente de rede virtual do Azure. 
* Uma VNet com o nome **onpremvnet** utilizado para imitar um centro de dados no local segmentado conforme listado abaixo.
  * **onpremsn1**. Sub-rede que contém uma máquina virtual (VM) em execução Ubuntu para imitar um servidor no local.
  * **onpremsn2**. Sub-rede que contenha uma VM com Ubuntu para imitar um computador no local, utilizado por um administrador.
* Não há uma aplicação virtual de firewall um com o nome **OPFW** no **onpremvnet** utilizado para manter um túnel para **azurevnet**.
* Uma VNet com o nome **azurevnet** segmentado conforme listado abaixo.
  * **azsn1**. Sub-rede de firewall externa usada exclusivamente para a firewall externa. Todo o tráfego de Internet ficará através desta sub-rede. Esta sub-rede contém apenas um NIC ligado à firewall externa.
  * **azsn2**. Sub-rede de front-end que aloja uma VM em execução como um servidor web que será acedido a partir da Internet.
  * **azsn3**. Sub-rede de back-end que aloja uma VM com um servidor de aplicação de back-end que poderá ser acedido pelo servidor web front-end.
  * **azsn4**. Sub-rede de gestão utilizado exclusivamente para fornecer acesso de gestão para todas as aplicações virtuais de firewall. Esta sub-rede contém apenas um NIC para cada aplicação virtual de firewall utilizada na solução.
  * **GatewaySubnet**. Híbridos do Azure ligação sub-rede necessária para o ExpressRoute e Gateway de VPN para fornecer conectividade entre as VNets do Azure e outras redes. 
* Existem 3 aparelhos virtuais de firewall no **azurevnet** rede. 
  * **AZF1**. Firewall externa exposto à Internet pública através da utilização de um recurso de endereço IP público no Azure. Terá de se certificar de que tem um modelo do Marketplace, ou diretamente a partir do seu fornecedor de aplicação que Aprovisiona uma aplicação virtual 3-NIC.
  * **AZF2**. Firewall interno utilizado para controlar o tráfego entre **azsn2** e **azsn3**. Também se trata de uma aplicação virtual 3-NIC.
  * **AZF3**. Firewall de gestão ser acedida pelos administradores do Centro de dados no local e ligado a uma sub-rede de gestão utilizada para gerir todas as aplicações de firewall. Pode encontrar modelos de aplicação virtual de 2 NIC no mercado, ou peça um diretamente a partir do fabricante do dispositivo.

## <a name="user-defined-routing-udr"></a>Encaminhamento (UDR) definido pelo utilizador
Cada sub-rede no Azure pode ser associado a uma tabela UDR utilizada para definir como tráfego iniciada nessa sub-rede é encaminhada. Não se for definidos nenhum UDRs, o Azure utiliza rotas predefinidas para permitir o tráfego para o fluxo a partir de uma sub-rede para outro. Para compreender melhor UDRs, visite [que são rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md).

Para garantir a comunicação é efetuada através do dispositivo de firewall corretas, com base no requisito de último acima, terá de criar a seguinte tabela de rota que contém UDRs no **azurevnet**.

### <a name="azgwudr"></a>azgwudr
Neste cenário, o tráfego apenas fluir no local para Azure será utilizado para gerir as firewalls estabelecendo ligação à **AZF3**, e que o tráfego tem de aceder através da firewall interna, **AZF2**. Por conseguinte, é necessária em apenas uma rota de **GatewaySubnet** conforme mostrado abaixo.

| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Permite que o tráfego no local aceder a firewall de gestão **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Permite que o tráfego para a sub-rede de back-end que aloja o servidor de aplicações através de **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Permite que todos os outros tráfegos ser encaminhada através de **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Permite que o tráfego **azsn2** fluxo do servidor de aplicação para o servidor Web através de **AZF2** |

Também tem de criar as tabelas de rotas para sub-redes no **onpremvnet** para imitar o Centro de dados no local.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Permite que o tráfego **onpremsn2** através de **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Permite que o tráfego para a sub-rede de segurança no Azure através de **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Permite que o tráfego **onpremsn1** através de **OPFW** |

## <a name="ip-forwarding"></a>Reencaminhamento IP
UDR e reencaminhamento IP são funcionalidades que pode utilizar em combinação para permitir que aplicações virtuais a serem utilizados para controlar o fluxo de tráfego numa VNet do Azure.  Uma aplicação virtual não é mais do que uma VM que executa uma aplicação utilizada para processar tráfego de rede de alguma forma, tal como uma firewall ou um dispositivo NAT.

Este VM de aplicação virtual deve ser capaz de receber tráfego de entrada que não esteja endereçada a si mesma. Para permitir que uma VM receba tráfego endereçado a outros destinos, tem de ativar o Reencaminhamento IP para a VM. Isto é uma definição do Azure, não se trata de uma definição no sistema operativo convidado. A aplicação virtual ainda tem de executar algum tipo de aplicação para processar o tráfego de entrada e encaminhá-lo adequadamente.

Para mais informações sobre o reencaminhamento IP, visite [que são rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md).

Por exemplo, imagine que tem a seguinte configuração numa vnet do Azure:

* Sub-rede **onpremsn1** contém uma VM chamada **onpremvm1**.
* Sub-rede **onpremsn2** contém uma VM chamada **onpremvm2**.
* Uma aplicação virtual com o nome **OPFW** está ligado à **onpremsn1** e **onpremsn2**.
* Uma rota definida pelo utilizador associados a **onpremsn1** Especifica que todo o tráfego para **onpremsn2** tem de ser enviadas para **OPFW**.

AT este ponto, se **onpremvm1** tenta estabelecer uma ligação com **onpremvm2**, será utilizado o UDR e será possível enviar o tráfego para **OPFW** como salto seguinte. Tenha em atenção que o destino do pacote real não está a ser alterado, ainda diz **onpremvm2** é o destino. 

Sem o reencaminhamento IP ativada para **OPFW**, a lógica da rede virtual do Azure irá remover os pacotes, uma vez que só permite que os pacotes enviados para uma VM, se o endereço IP da VM é o destino para o pacote.

Com o reencaminhamento IP, a lógica da rede virtual do Azure irá reencaminhar os pacotes para OPFW, sem alterar o respetivo endereço de destino original. **OPFW** tem de processar os pacotes e determinar o que fazer com as mesmas.

Para o cenário acima para funcionar, tem de ativar o reencaminhamento IP em NICs para **OPFW**, **AZF1**, **AZF2**, e **AZF3** que são utilizadas para encaminhamento (todos os NICs exceto as ligada à sub-rede de gestão). 

## <a name="firewall-rules"></a>Regras da Firewall
Como descrito acima, o reencaminhamento IP só garante a pacotes são enviados para as aplicações virtuais. O dispositivo ainda tem de decidir o que fazer com esses pacotes. No cenário acima, terá de criar as regras seguintes nos dispositivos:

### <a name="opfw"></a>OPFW
OPFW representa um dispositivo no local que contém as seguintes regras:

* **Rota**: todo o tráfego para 10.0.0.0/16 (**azurevnet**) deve ser enviado através de túnel **ONPREMAZURE**.
* **Política**: permitir todo o tráfego de bidirecional entre **port2** e **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 representa uma aplicação virtual do Azure que contém as seguintes regras:

* **Política**: permitir todo o tráfego de bidirecional entre **port1** e **port2**.

### <a name="azf2"></a>AZF2
AZF2 representa uma aplicação virtual do Azure que contém as seguintes regras:

* **Rota**: todo o tráfego para 10.0.0.0/16 (**onpremvnet**) deve ser enviado para o gateway do Azure endereço IP (ou seja, 10.0.0.1) através de **port1**.
* **Política**: permitir todo o tráfego de bidirecional entre **port1** e **port2**.

## <a name="network-security-groups-nsgs"></a>Grupos de segurança de rede (NSGs)
Neste cenário, não estão a ser utilizados NSGs. No entanto, pode aplicar NSGs para cada sub-rede para restringir o tráfego de entrada e saído. Por exemplo, pode aplicar as seguintes regras NSG para sub-rede FW externa.

**Entrada**

* Permitir todo o tráfego TCP a partir da Internet para a porta 80 em qualquer VM na sub-rede.
* Negar todo o restante tráfego a partir da Internet.

**Saída**

* Negar todo o tráfego à Internet.

## <a name="high-level-steps"></a>Passos de nível elevados
Para implementar este cenário, siga os passos de alto nível abaixo.

1. Início de sessão para a sua subscrição do Azure.
2. Se pretender implementar uma VNet para imitar a rede no local, aprovisionar os recursos que fazem parte do **ONPREMRG**.
3. Aprovisionar os recursos que fazem parte do **AZURERG**.
4. Aprovisionar o túnel do **onpremvnet** para **azurevnet**.
5. Depois de todos os recursos são aprovisionados, inicie sessão no **onpremvm2** e enviar um ping 10.0.3.101 para testar a conectividade entre **onpremsn2** e **azsn3**.

