---
title: "Exemplo de rede de Perímetro – criar uma rede de Perímetro a proteger a redes com uma Firewall, UDR e NSG | Microsoft Docs"
description: "Criar uma rede de Perímetro com uma Firewall, definidas pelo utilizador (UDR) encaminhamento e grupos de segurança de rede (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: fdb3c5cbd3acee90386352c6f180a71aa81f54fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemplo 3 – criar uma rede de Perímetro a proteger a redes com um NSG, UDR e Firewall
[Regressar à página de segurança limites melhores práticas][HOME]

Neste exemplo irá criar uma rede de Perímetro com uma firewall, quatro servidores windows, encaminhamento de definido pelo utilizador, o reencaminhamento IP e grupos de segurança de rede. Também irá orientá-através de cada um dos comandos relevantes para fornecer uma compreensão mais aprofundada de cada passo. Há também uma secção de cenário de tráfego para fornecer um aprofundada passo a passo como procede de tráfego através das camadas de defesa na rede de Perímetro. Por fim, as referências de secção é o código de conclusão e instruções para criar este ambiente de teste e experimentar vários cenários. 

![Rede de Perímetro bidirecional com NVA, NSG e UDR][1]

## <a name="environment-setup"></a>Configuração do ambiente
Neste exemplo, não há uma subscrição que contém o seguinte:

* Três serviços em nuvem: "SecSvc001", "FrontEnd001" e "BackEnd001"
* Uma rede Virtual "CorpNetwork", com três sub-redes: "SecNet", "FrontEnd" e "BackEnd"
* Uma aplicação virtual de rede, neste exemplo de uma firewall, ligado à sub-rede SecNet
* Um servidor do Windows que representa um servidor de web de aplicação ("IIS01")
* Dois windows servers que representam aplicações novamente terminar servidores ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")

Na secção referências abaixo há um script do PowerShell que irá criar a maior parte do ambiente descrito acima. Criação de VMs e redes virtuais, embora são efetuadas pelo script de exemplo, não são descritas em detalhe neste documento.

Para criar o ambiente:

1. Guarde o ficheiro de xml de configuração de rede incluído na secção de referências (atualizado com os nomes e localização e IP endereços para fazer corresponder o cenário determinado)
2. Atualizar as variáveis de utilizador no script para fazer corresponder o ambiente que de script está a ser executado contra (subscrições, nomes de serviço, etc.)
3. Execute o script do PowerShell

**Tenha em atenção**: região signified no PowerShell script tem de corresponder à região signified no ficheiro xml de configuração de rede.

Assim que o script é executado com êxito os seguintes passos de scripts de pós-implementação podem ser demorados:

1. Configurar as regras de firewall, isto é explicado na secção abaixo intitulada: Descrição da regra de Firewall.
2. Opcionalmente, na secção de referências são dois scripts para configurar o servidor web e o servidor de aplicação com uma aplicação web simples para permitir testes com esta configuração de rede de Perímetro.

Assim que o script é executado com êxito a firewall regras terá de ser concluída, isto é explicado na secção intitulada: regras de Firewall.

## <a name="user-defined-routing-udr"></a>Encaminhamento (UDR) definido pelo utilizador
Por predefinição, as rotas de sistema seguinte são definidas como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

O VNETLocal é sempre o prefixo de endereço definido da VNet para essa rede específica (ie, deixará de VNet a VNet, dependendo de como cada VNet específico está definido). As rotas de sistema restantes são estáticas e predefinido conforme apresentado acima.

Idêntica à prioridade, as rotas são processadas através do método de correspondência de prefixo mais longo (LPM), assim a rota mais específica na tabela seria aplicada a um endereço de destino indicado.

Por conseguinte, o tráfego (por exemplo, para o servidor de DNS01, 10.0.2.4) concebido para a rede local (10.0.0.0/16) seria ser encaminhado entre a VNet para o destino devido a rota 10.0.0.0/16. Por outras palavras, para 10.0.2.4, a rota 10.0.0.0/16 é a rota mais específica, apesar do 10.0.0.0/8 e 0.0.0.0/0 também é possível aplicar, mas uma vez que são menos específico não afetam este tráfego. Deste modo, o tráfego para 10.0.2.4 teria um salto seguinte da local VNet e simplesmente encaminhar para o destino.

Se o tráfego foi concebido para 10.1.1.1, por exemplo, a rota 10.0.0.0/16 não se aplicam, mas o 10.0.0.0/8 seria mais específico e isto seria o tráfego removido ("negra holed"), uma vez que o próximo salto é nulo. 

Se o destino não se aplicou a nenhum dos prefixos nulo ou os prefixos de VNETLocal, em seguida, deverá seguir menos específicos encaminhar 0.0.0.0/0 e ser encaminhado à Internet como salto seguinte e, consequentemente, o limite de internet do Azure.

Se existirem dois prefixos idênticos na tabela de rota, segue-se a ordem de preferência, com base no atributo "origem" rotas:

1. "VirtualAppliance" = uma rota de definidas de utilizador adicionadas manualmente à tabela de
2. "O VPNGateway" = uma rota de dinâmico (BGP quando utilizado com redes híbrida), adicionada por um protocolo de rede dinâmico, estas rotas podem alterar ao longo do tempo, o protocolo dinâmico reflete automaticamente as alterações na rede em modo de peering
3. "Predefinido" = as rotas de sistema, a VNet local e as entradas estáticas, conforme mostrado na tabela de rota acima.

> [!NOTE]
> Agora, pode utilizar utilizador definido encaminhamento (UDR) com o ExpressRoute e Gateways de VPN para forçar a saída e entrada em vários locais tráfego para ser encaminhados para uma aplicação virtual de rede (NVA).
> 
> 

#### <a name="creating-the-local-routes"></a>Criar as rotas locais
Neste exemplo, são necessárias duas tabelas de encaminhamento, cada um das sub-redes de front-end e back-end. Cada tabela é carregada com rotas estáticas adequadas para a sub-rede especificada. Para efeitos deste exemplo, cada tabela tenha três rotas:

1. Tráfego de sub-rede local com nenhuma próximo salto definido para permitir tráfego de sub-rede local para ignorar a firewall
2. Tráfego de rede virtual com um salto seguinte definido como firewall, isto substitui a regra predefinida que permita o tráfego de VNet local encaminhar diretamente
3. Todos os restantes o tráfego (0/0) com um salto seguinte definida como a firewall

Depois das tabelas de encaminhamento são criadas estes estão vinculados às suas sub-redes. Para a sub-rede do front-end tabela de encaminhamento, uma vez criada e vinculado à sub-rede deve ter o seguinte aspeto:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Neste exemplo, os comandos seguintes são utilizados para criar a tabela de rotas, adicione uma rota definida pelo utilizador e, em seguida, vincular a tabela de rotas para uma sub-rede (nota; quaisquer itens abaixo que começa com um cifrão (por ex.: $BESubnet) são definidos pelo utilizador variáveis do script no referência secção deste documento):

1. Primeiro a tabela de encaminhamento base tem de ser criada. Este fragmento mostra a criação da tabela para a sub-rede de back-end. O script, uma tabela correspondente também é criada para a sub-rede do front-end.
   
     Novo AzureRouteTable-nome $BERouteTableName '
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. Depois da tabela de rotas é criada, podem ser adicionadas rotas definidas pelo utilizador de específico. Deste abreviada, todo o tráfego (0.0.0.0/0) será encaminhado através de aplicação virtual (uma variável, $VMIP [0], é utilizada para transmitir o endereço IP atribuído quando a aplicação virtual foi criada anteriormente no script). O script, uma regra correspondente também é criada na tabela de front-end.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. A entrada de rota acima irá substituir a "0.0.0.0/0" rota predefinida, mas a regra predefinida do 10.0.0.0/16 ainda existente que permita o tráfego dentro da VNet para encaminhar diretamente para o destino e não para a aplicação Virtual de rede. Para correto este comportamento a regra de seguir tem de ser adicionado.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. Neste momento, não há uma opção para ser efetuada. Com as rotas de dois acima todo o tráfego irá encaminhar para a firewall para avaliação, mesmo que o tráfego numa sub-rede única. Isto pode ser pretendido, no entanto, para permitir o tráfego dentro de uma sub-rede para encaminhar localmente sem o intervenção do firewall uma terceira, é possível adicionar a regra muito específica. Esta rota Estados destine de qualquer endereço de sub-rede local pode apenas existe encaminhar diretamente (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Por fim, com a tabela de encaminhamento criadas e preenchidas com uma rotas definidas pelo utilizador, a tabela tem agora de estar vinculada a uma sub-rede. No script, também se encontra vinculada a tabela de rotas de front-end para a sub-rede do front-end. Eis o script de enlace para a sub-rede de back-end.
   
     Conjunto AzureSubnetRouteTable - VirtualNetworkName $VNetName '
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Reencaminhamento IP
Uma funcionalidade de complemento para UDR, é o reencaminhamento IP. Esta é uma definição de uma aplicação Virtual que permite receba tráfego endereçado não especificamente para a aplicação e, em seguida, reencaminhar o tráfego para o destino ultimate.

Por exemplo, se o tráfego de AppVM01 faz um pedido para o servidor de DNS01 UDR seria encaminhar isto à firewall. Com o reencaminhamento IP ativada, o tráfego para o destino de DNS01 (10.0.2.4) será aceite pelo dispositivo de (10.0.0.4) e, em seguida, é reencaminhado para o destino ultimate (10.0.2.4). Sem o reencaminhamento IP ativada na Firewall, o tráfego seria não aceite pelo dispositivo de apesar da tabela de rota tem de firewall como salto seguinte. 

> [!IMPORTANT]
> É fundamental para lembrar de ativar o reencaminhamento IP em conjunto com o encaminhamento de definido pelo utilizador.
> 
> 

Configurar o reencaminhamento IP é um comando único e pode ser feito no momento de criação de VM. Para que o fluxo deste exemplo o fragmento de código está perto do final do script e agrupado com os comandos UDR:

1. Chamar a instância VM que está a sua aplicação virtual, a firewall neste caso e ativar o reencaminhamento IP (nota; qualquer item no início vermelho com um cifrão (por ex.: $VMName[0]) é uma variável de definidas pelo utilizador do script na secção de referência deste documento. Zero entre parênteses Retos, [0], representa a primeira VM na matriz de VMs, para o script de exemplo trabalhar sem modificação, a primeira VM (VM 0) tem de ser a firewall):
   
     Get-AzureVM-nome $VMName [0] - ServiceName $ServiceName [0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Grupos de Segurança de Rede (NSG)
Neste exemplo, um grupo NSG está incorporado e, em seguida, carregado com uma única regra. Este grupo, em seguida, está vinculado apenas para as sub-redes de front-end e back-end (não o SecNet). Modo declarativo está a ser criada a seguinte regra:

1. Qualquer tráfego (todas as portas) da Internet para a VNet completa (todas as sub-redes) é negado

Embora NSGs são utilizados neste exemplo, o objetivo principal é como uma camada secundária de defesa contra manual configuração incorreta. Queremos bloquear todo o tráfego de entrada da internet para qualquer o front-end ou sub-redes de back-end, o tráfego devem apenas flui através da sub-rede SecNet à firewall do (e, em seguida, se adequado para o front-end ou back-end sub-redes). Plus, com as regras UDR no local, qualquer tráfego que torná-lo para as sub-redes de front-end ou back-end seria direcionado enviados para a firewall (graças ao UDR). A firewall a é apresentado tal como um fluxo assimétrico e deverá remover o tráfego de saída. Deste modo, existem três camadas de segurança proteger as sub-redes de front-end e back-end; 1) não existem pontos finais abertos no FrontEnd001 e BackEnd001 serviços em nuvem, 2) os NSGs negar o tráfego da Internet, 3) o tráfego de assimétrico de remover de firewall.

Um ponto de interessante sobre o grupo de segurança de rede neste exemplo é que contém apenas uma regra, abaixo, que está a negar o tráfego de internet para toda a rede virtual que inclui a sub-rede de segurança. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

No entanto, uma vez que o NSG só está vinculado às sub-redes de front-end e back-end, a regra não é processada sobre o tráfego de entrada para a sub-rede de segurança. Como resultado, apesar da regra NSG não indica nenhum tráfego de Internet para qualquer endereço vnet, porque o NSG nunca foi vinculado à sub-rede de segurança, o tráfego irá fluir para a sub-rede de segurança.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Regras da Firewall
Na firewall, as regras de reencaminhamento terá de ser criado. Uma vez que a firewall está a bloquear ou o reencaminhamento toda a entrada, saído e a intra VNet tráfego são necessárias várias regras de firewall. Além disso, todo o tráfego de entrada será atingiu o endereço IP público do serviço de segurança (em portas diferentes), para ser processado pela firewall. Uma melhor prática é para os fluxos lógicos antes de configurar as sub-redes de diagrama e regras de firewall para evitar rework mais tarde. A figura seguinte é uma vista lógica das regras de firewall para este exemplo:

![Vista lógica das regras de Firewall][2]

> [!NOTE]
> As portas de gestão com base no dispositivo Virtual de rede utilizados, irão variar. Neste exemplo que é referenciada um Barracuda NextGen Firewall que utiliza as portas 22, 801 e 807. Consulte a documentação do fornecedor de aplicação para localizar as portas exatas utilizadas para a gestão do dispositivo a ser utilizado.
> 
> 

### <a name="logical-rule-description"></a>Descrição da lógica de regra
A sub-rede de segurança não é apresentada no diagrama lógico acima, uma vez que a firewall do recurso apenas essa sub-rede, e este diagrama é que mostra as regras de firewall e como logicamente permitir ou negar fluxos de tráfego e não o caminho encaminhado real. Além disso, as portas externas, selecionadas para o tráfego RDP são superior ranged portas (8014 – 8026) e não foram selecionadas para um pouco alinhar com os dois últimos octetos do endereço IP local para o facilitar a leitura mais fácil (por exemplo, endereço de servidor local 10.0.1.4 está associado a porta externa 8014), no entanto qualquer superiores portas em conflito não pode ser utilizadas.

Para este exemplo, é necessário 7 tipos de regras, estes tipos de regra são descritos da seguinte forma:

* Regras de externas (para o tráfego de entrada):
  1. Regra de firewall de gestão: Esta regra de redirecionamento da aplicação permite que o tráfego passar para as portas de gestão do dispositivo virtual de rede.
  2. Regras de RDP (para cada servidor do windows): estas quatro regras (um para cada servidor) irão permitir a gestão dos servidores individuais através do RDP. Também pode ser incluída para uma regra, consoante as capacidades do dispositivo Virtual de rede que está a ser utilizado.
  3. As regras de tráfego de aplicação: Existem duas regras de tráfego de aplicação, o primeiro para o tráfego da web de front-end e o segundo para o tráfego de back-end (ex servidor web para a camada de dados). A configuração destas regras será dependem a arquitetura de rede (em que os servidores são colocados) e fluxos de tráfego (a direção fluxos de tráfego e que portas são utilizadas).
     * A primeira regra irá permitir o tráfego de aplicação real alcançar o servidor de aplicação. Enquanto outras regras permitem a segurança, gestão, etc., as regras de aplicação são que permitir que os utilizadores externos ou serviços aceder a aplicação (ões). Neste exemplo, existe um servidor web único na porta 80, deste modo, uma única aplicação regra de firewall irá redirecionar o tráfego de entrada para o IP externo, para o endereço IP da interno de servidores web. A sessão de tráfego redirecionada seria possível NAT que tiveram de servidor interno.
     * A segunda regra de tráfego de aplicação é a regra de back-end para permitir que o servidor Web para que comunique com o servidor de AppVM01 (mas não AppVM02) através de qualquer porta.
* Regras internas (intra-VNet para o tráfego de)
  1. Saída para a regra de Internet: esta regra irá permitir tráfego de qualquer rede para passar para as redes selecionadas. Esta regra é, normalmente, uma regra predefinida já na firewall, mas num estado desativado. Esta regra deve ser ativada para este exemplo.
  2. Regra DNS: Esta regra permite apenas tráfego DNS (porta 53) passar para o servidor DNS. Para este ambiente que maioria do tráfego do front-end para o back-end está bloqueada, esta regra permite especificamente DNS de qualquer sub-rede local.
  3. Regra de sub-rede a sub-rede: esta regra é permitir que qualquer servidor na sub-rede de back-end para ligar a nenhum servidor na sub-rede do front-end (mas não o inverso).
* Obter regra isento de falhas (para o tráfego que não cumpre uma das soluções anteriores):
  1. Negar a regra de tráfego de todos os: Este deve ser sempre a regra final (em termos de prioridade) e como tal, se o tráfego flui falha corresponder a qualquer uma das regras anteriores que serão ignorado por esta regra. Esta é uma regra predefinida e, normalmente, é ativado, sem modificações geralmente são necessários.

> [!TIP]
> A segunda regra de tráfego de aplicações, qualquer porta é permitida para fácil deste exemplo, num cenário real a porta mais específica em intervalos de endereços devem ser utilizados para reduzir a superfície de ataque desta regra.
> 
> 

<br />

> [!IMPORTANT]
> Depois de todas as regras acima são criadas, é importante rever a prioridade de cada regra para garantir que o tráfego será permitido ou negado conforme pretendido. Neste exemplo, as regras são ordem de prioridade. É fácil bloqueado fora da firewall devido a regras incorrectamente ordenadas. No mínimo, certifique-se a gestão da firewall do próprio sempre a regra de prioridade mais alta absoluto.
> 
> 

### <a name="rule-prerequisites"></a>Pré-requisitos de regra
Um pré-requisito para a Máquina Virtual com a firewall são pontos finais públicos. Para a firewall para processar o tráfego de pontos finais públicos adequados tem de estar abertos. Existem três tipos de tráfego neste exemplo; Tráfego RDP de 1) tráfego de gestão para o controlo da firewall e regras de firewall, 2) para controlar o tráfego de aplicação de 3) e de servidores do windows. Estas são as três colunas tipos de tráfego no canto superior metade da vista lógica das regras de firewall acima.

> [!IMPORTANT]
> Uma chave takeway aqui é lembrar-se de que **todos os** tráfego ficará através da firewall. Por isso, para o ambiente de trabalho remoto para o servidor de IIS01, apesar de estar no serviço de nuvem de Front-End e a sub-rede do Front-End, para aceder a este servidor iremos tem RDP para a firewall na porta 8014 e, em seguida, permitir que a firewall para encaminhar o pedido RDP internamente para Por RDP IIS01 t. Botão de "Ligar" no portal do Azure poderá não funciona porque não existe nenhum caminho RDP direto para IIS01 (como pode ver no portal). Isto significa que todas as ligações da internet será para o serviço de segurança e uma porta, por exemplo, secscv001.cloudapp.net:xxxx (referência o diagrama acima para o mapeamento de porta de externas e internas IP e a porta).
> 
> 

Um ponto final pode ser aberta uma no momento da criação de VM ou post compilação como é efetuada no script de exemplo e apresentado abaixo neste fragmento de código (nota; qualquer item que começa com um cifrão (por ex.: $VMName[$i]) é uma variável de definidas pelo utilizador do script no sectio de referência n deste documento. O "$i" entre parênteses Retos, [$i] representa o número de matriz de uma VM específica numa matriz de VMs):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Apesar de não claramente apresentado aqui devido a utilização de variáveis, mas pontos finais são **apenas** aberto segurança serviço em nuvem. Isto é para garantir que todo o tráfego de entrada é processado (encaminhamento, NAT tinha, removido) pela firewall.

Um cliente de gestão terão de ser instalada num PC para gerir a firewall e criar as configurações necessárias. Consulte o fornecedor de documentação da sua firewall (ou outro NVA) sobre como gerir o dispositivo. O resto nesta secção e na secção seguinte, criação de regras de Firewall, descreve a configuração da firewall, através do cliente de gestão de fornecedores (ou seja, não o portal do Azure ou o PowerShell).

Instruções para a transferência de cliente e os ligam à Barracuda utilizado neste exemplo, podem ser encontradas aqui: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Assim que tiver sessão iniciada para a firewall, mas antes de criar regras de firewall, existem duas classes de objetos de pré-requisitos que podem efetuar a criação de regras mais fácil; Objetos de rede e o serviço.

Neste exemplo, três objetos de rede com o nome devem ser definida (uma para a sub-rede do front-end e a sub-rede de back-end, também um objeto de rede para o endereço IP do servidor DNS). Para criar uma rede nomeada; a partir do dashboard de cliente Barracuda NG administrador, navegue até ao separador de configuração, na secção de configuração operacional Ruleset, em seguida, clique em "Redes" sob o menu de objetos de Firewall, clique em novo no menu de editar redes. O objeto de rede pode agora ser criado, adicionando o nome e o prefixo:

![Criar um objeto de rede de front-end][3]

Esta ação irá criar uma rede nomeada para a sub-rede do front-end, deve ser criado um objeto de semelhante para a sub-rede de back-end, bem como. Agora as sub-redes podem ser referenciadas mais facilmente por nome nas regras da firewall.

Para o objeto de servidor DNS:

![Criar um objeto de servidor DNS][4]

Esta referência do endereço IP único será utilizada numa regra de DNS mais tarde no documento.

Os segundo objetos de pré-requisitos são serviços. Estes representarão as portas de ligação do RDP para cada servidor. Uma vez que o objeto de serviço RDP existente está vinculado à porta RDP predefinido, 3389, novos serviços podem ser criados para permitir tráfego das portas externos (8014 8026). As novas portas também podem ser adicionadas para o serviço RDP existente, mas para facilitar a demonstração, pode ser criada uma regra individuais para cada servidor. Para criar uma nova regra RDP para um servidor; a partir do dashboard de cliente Barracuda NG administrador, navegue até ao separador de configuração, na secção de configuração operacional clique Ruleset, em seguida, clique em "Serviços" sob o menu de objetos de Firewall, desloque para baixo a lista de serviços e selecione o serviço de "RDP". Clique com botão direito e selecione cópia, em seguida, clique com botão direito e selecione colar. Agora é um objeto de serviço de RDP Copy1 que pode ser editado. Faça duplo clique RDP Copy1 e selecione editar, o objeto de serviço editar janela será destaque conforme mostrado aqui:

![Cópia de regra predefinida do RDP][5]

Os valores, em seguida, podem ser editados para representar o serviço RDP para um servidor específico. Para AppVM01 a regra predefinida do RDP acima deverá ser modificada para refletir um novo nome de serviço, a descrição e a porta RDP externa utilizado no diagrama figura 8 (Nota: as portas são alteradas da predefinição RDP de 3389 para a porta externa que está a ser utilizada para este servidor específico no caso de AppVM01 a porta externa é 8025) o serviço modificado é mostrado abaixo:

![Regra de AppVM01][6]

Este processo deve ser repetido a criação de serviços de RDP para os restantes servidores; AppVM02, DNS01 e IIS01. A criação destes serviços faz com que a criação de regra mais simples e mais óbvios na secção seguinte.

> [!NOTE]
> Um serviço RDP para a Firewall não é necessária por duas razões; 1) primeiro a firewall VM é uma imagem de baseadas em Linux, de modo a SSH seria utilizado na porta 22 para gestão de VM em vez de RDP e a porta 22 2) e duas portas de gestão são permitidas na primeira regra de gestão descrita abaixo, para permitir a conectividade da gestão.
> 
> 

### <a name="firewall-rules-creation"></a>Criação de regras de firewall
Existem três tipos de regras de firewall utilizadas neste exemplo, têm ícones distintos:

A regra de redirecionamento da aplicação: ![ícone de redirecionamento da aplicação][7]

A regra NAT de destino: ![ícone de NAT de destino][8]

A regra de Pass: ![passar ícone][9]

Obter mais informações sobre estas regras podem ser encontradas em Barracuda web site.

Para criar as regras seguintes (ou certifique-se de regras de predefinidas existente), a partir do dashboard de cliente Barracuda NG administrador, navegue até ao separador de configuração, na configuração operacional secção clique Ruleset. Uma grelha chamada, "Regras de principal" mostrará as regras existentes do Active Directory e desativadas neste firewall. No canto superior direito desta grelha é um pequeno, verde "+" botão, clique em esta opção para criar uma nova regra (Nota: a firewall pode ser "bloqueada" para as alterações, se vir um botão marcada "Bloquear" e não é possível criar ou editar regras, clicar neste botão para o conjunto de regras "desbloquear" e  permitir a edição). Se pretender editar uma regra existente, selecione essa regra, clique com botão direito e selecione Editar regra.

Assim que as regras são criadas e/ou modificadas, devem ser enviadas para a firewall e, em seguida, ativar, se tal não as alterações de regra não entrará em vigor. O processo de emissão e a ativação é descrito abaixo as descrições de regra de detalhes.

As especificações de cada regra necessário para concluir este exemplo são descritas da seguinte forma:

* **Gestão de regra de firewall**: redirecionamento de aplicação esta regra permite que o tráfego passar para as portas de gestão do dispositivo virtual de rede, neste exemplo, um Barracuda NextGen Firewall. As portas de gestão são 801, 807 e, opcionalmente, 22. As portas externas e internas são as mesmas (ou seja, nenhuma conversão de porta). Esta regra de configuração-MGMT-acesso, é uma regra predefinida e ativado por predefinição (na versão de Barracuda NextGen Firewall 6.1).
  
    ![Regra de firewall de gestão][10]

> [!TIP]
> O espaço de endereço de origem nesta regra é aplicável, se os intervalos de endereços IP de gestão são conhecidos, reduzir este âmbito seria também reduzir a superfície de ataque para as portas de gestão.
> 
> 

* **Regras de RDP**: regras NAT de destino estas irão permitir a gestão dos servidores individuais através do RDP.
  Existem quatro campos críticos necessários para criar esta regra:
  
  1. Origem – para permitir RDP de qualquer lugar, a referência "Qualquer" é utilizada no campo de origem.
  2. Serviço – utilize o objecto de serviço apropriado criado anteriormente, neste caso, "AppVM01 RDP", as portas externas redirecionar para o endereço IP local do servidores e a porta 3386 (a porta predefinida RDP). Esta regra específica é para o acesso RDP AppVM01.
  3. Deve ser o destino – o *local porta na firewall*, "DCHP 1 Local IP" ou eth0 se utilizar IPs estáticos. O número de ordinal (eth0 eth1, etc.) pode ser diferente se o dispositivo de rede tem várias interfaces de locais. Esta é a porta a firewall está a enviar limite de (pode ser o mesmo que a porta de receção), o destino de encaminhadas real é no campo de lista de destino.
  4. Redirecionamento – esta secção indica a aplicação virtual onde este tráfego de redirecionamento, em última análise. O redirecionamento mais simples é colocar o IP e a porta (opcional) no campo de lista de destino. Se nenhuma porta é utilizada a porta de destino do pedido de entrada será utilizado (ie nenhuma conversão), se uma porta está designada a porta será também NAT seria juntamente com o IP de endereços.
     
     ![Regra de RDP de firewall][11]
     
     Um total de quatro regras RDP terá de ser criada: 
     
     | Nome da Regra | Servidor | Serviço | Lista de destino |
     | --- | --- | --- | --- |
     | RDP para IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP para DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP para AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
     | RDP para AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> Restrição para baixo o âmbito dos campos de origem e o serviço irá reduzir a superfície de ataque. O âmbito mais limitado que irá permitir a funcionalidade deve ser utilizado.
> 
> 

* **As regras de tráfego de aplicação**: Existem duas regras de tráfego de aplicação, o primeiro para o tráfego da web de front-end e o segundo para o tráfego de back-end (ex servidor web para a camada de dados). Estas regras serão dependem a arquitetura de rede (em que os servidores são colocados) e fluxos de tráfego (a direção fluxos de tráfego e que portas são utilizadas).
  
    Primeiro abordados é a regra de front-end para o tráfego web:
  
    ![Regra de firewall Web][12]
  
    Esta regra NAT de destino permite o tráfego de aplicação real alcançar o servidor de aplicação. Enquanto outras regras permitem a segurança, gestão, etc., as regras de aplicação são que permitir que os utilizadores externos ou serviços aceder a aplicação (ões). Neste exemplo, existe um servidor web único na porta 80, deste modo, a única aplicação regra de firewall irá redirecionar o tráfego de entrada para o IP externo, para o endereço IP da interno de servidores web.
  
    **Tenha em atenção**: que não há nenhuma porta atribuída no campo de lista de destino, deste modo, a porta de entrada 80 (ou 443 para o serviço selecionado) será utilizada o redirecionamento do servidor web. Se o servidor web está à escuta numa porta diferente, por exemplo a porta 8080, foi possível atualizar o campo de lista de destino para 10.0.1.4:8080 para permitir, bem como o redirecionamento de porta.
  
    A seguinte regra de tráfego de aplicação é a regra de back-end para permitir que o servidor Web para que comunique com o servidor de AppVM01 (mas não AppVM02) através de qualquer serviço:
  
    ![Regra de AppVM01 de firewall][13]
  
    Esta regra de Pass permite que qualquer servidor IIS a sub-rede do front-end para alcançar o AppVM01 (endereço IP 10.0.2.5) em qualquer porta a utilizar qualquer protocolo para aceder a dados necessários para a aplicação web.
  
    Nesta captura de ecrã um "\<explícita dest\>" é utilizado no campo de destino para indicar 10.0.2.5 como destino. Tal poderia dever-explícita conforme mostrado ou uma chamada objeto de rede (uma vez que foi realizado nos pré-requisitos para o servidor DNS). Trata-se até o administrador da firewall relativamente a qual o método irá ser utilizado. Para adicionar 10.0.2.5 como um Explict Desitnation, faça duplo clique na primeira linha em branco em \<explícita dest\> e introduza o endereço na janela que aparece.
  
    Com esta regra de passar sem NAT é necessária uma vez que este é tráfego interno, pelo que o método de ligação pode ser definido como "Não realizar o SNAT".
  
    **Tenha em atenção**: rede de origem a esta regra é qualquer recurso na sub-rede do front-end, se houver apenas um ou um número específico conhecido dos servidores web, um recurso de objeto de rede foi criado para ser mais específico para esses endereços IP exatos em vez do toda a sub-rede de front-end.

> [!TIP]
> Esta regra utiliza o serviço "Nenhum" para facilitar a aplicação de exemplo configurar e utilizar, este também permitirá ICMPv4 (ping) numa única regra. No entanto, isto não é uma prática recomendada. Devem ser narrowed as portas e protocolos ("serviços") para o mínimo possível que permite que a operação de aplicação reduzir a superfície de ataque entre este limite.
> 
> 

<br />

> [!TIP]
> Apesar desta regra mostra uma referência explícita dest a ser utilizada, uma abordagem consistente deve ser utilizada em toda a configuração da firewall. Recomenda-se que o objeto com o nome de rede utilizados em toda para facilitar a leitura mais fácil e suporte. -Dest explícita é utilizado aqui apenas para mostrar um método alternativo de referência e não é, geralmente, recomendado (especialmente de configurações complexas).
> 
> 

* **Saída para a regra de Internet**: passar esta regra irá permitir tráfego de qualquer rede de origem para passar para as redes de destino selecionadas. Esta regra é uma regra predefinida normalmente já na Barracuda NextGen firewall, mas está num estado desativado. Clicar nesta regra pode aceder de comando de ativar a regra. A regra mostrada aqui foi modificada para adicionar as duas sub-redes locais que foram criadas como referência na secção pré-requisitos deste documento para o atributo de origem desta regra.
  
    ![Regra de saída de firewall][14]
* **Regra de DNS**: passar esta regra permite apenas tráfego DNS (porta 53) passar para o servidor DNS. Para este ambiente que maioria do tráfego do front-end para o back-end está bloqueada, esta regra permite especificamente DNS.
  
    ![Regra de firewall DNS][15]
  
    **Tenha em atenção**: este ecrã captura o método de ligação é incluída. Uma vez que esta regra destina-se a IP internos para tráfego de endereços IP interno, não NATing é necessária, o método de ligação é definido para "Realizar o SNAT não" para esta regra de Pass.
* **Regra de sub-rede para sub-rede**: passar esta regra é uma regra predefinida que tenha sido ativada e modificada para permitir que qualquer servidor na sub-rede de back-end para ligar a nenhum servidor na sub-rede do front-end. Esta regra é o tráfego de todos os interno para que o método de ligação podem ser definido para realizar o SNAT não.
  
    ![Regra de VNet dentro da firewall][16]
  
    **Tenha em atenção**: A caixa de verificação bidirecional não é verificada (nem é selecionado na maioria das regras), isto é significativo para esta regra em que este faz com que esta regra de "um unidirecional", uma ligação pode ser iniciada a partir da sub-rede de back-end à rede front-end, mas não a inversa. Se essa caixa de verificação foi verificada, esta regra seria ativar o tráfego de bidirecional, que, da nossa diagrama lógico, não for pretendido.
* **Negar a regra de tráfego de todos os**: deve ser sempre a regra final (em termos de prioridade), e como tal, se o tráfego flui falhar para corresponder a qualquer um dos precedente as regras serão ignorado por esta regra. Esta é uma regra predefinida e, normalmente, é ativado, sem modificações geralmente são necessários. 
  
    ![Regra de negação de firewall][17]

> [!IMPORTANT]
> Depois de todas as regras acima são criadas, é importante rever a prioridade de cada regra para garantir que o tráfego será permitido ou negado conforme pretendido. Neste exemplo, as regras são pela ordem que devem aparecer na grelha de reencaminhamento de regras no cliente de gestão Barracuda principal.
> 
> 

## <a name="rule-activation"></a>Ativação de regra
Com o ruleset modificado a especificação do diagrama lógica, o ruleset tem de ser carregado para a firewall e, em seguida, é ativado.

![Ativação da regra de firewall][18]

O canto superior direito do cliente de gestão estão num cluster de botões. Clique no botão "Enviar alterações" para enviar as regras de modificação para a firewall e, em seguida, clique no botão "Ativar".

Com a ativação de ruleset a firewall a compilação de ambiente este exemplo está concluída.

## <a name="traffic-scenarios"></a>Cenários de tráfego
> [!IMPORTANT]
> É uma chave takeway lembrar-se de que **todos os** tráfego ficará através da firewall. Por isso, para o ambiente de trabalho remoto para o servidor de IIS01, apesar de estar no serviço de nuvem de Front-End e a sub-rede do Front-End, para aceder a este servidor iremos tem RDP para a firewall na porta 8014 e, em seguida, permitir que a firewall para encaminhar o pedido RDP internamente para Por RDP IIS01 t. Botão de "Ligar" no portal do Azure poderá não funciona porque não existe nenhum caminho RDP direto para IIS01 (como pode ver no portal). Isto significa que todas as ligações da internet será para o serviço de segurança e uma porta, por exemplo, secscv001.cloudapp.net:xxxx.
> 
> 

Nestes cenários, as seguintes regras de firewall devem ser implementados:

1. Gestão da firewall
2. RDP para IIS01
3. RDP para DNS01
4. RDP para AppVM01
5. RDP para AppVM02
6. Tráfego de aplicação na Web
7. Tráfego de aplicação para AppVM01
8. Saída à Internet
9. Front-end para DNS01
10. Tráfego de sub-rede intra (back-end para front-end apenas)
11. Negar tudo

O ruleset firewall real provavelmente terá muitos outras regras além destas, as regras em quaisquer firewall determinado também irão ter números de prioridade diferentes dos listados aqui que. Esta lista e números associados estão a fornecer relevância entre apenas estas eleven regras e a prioridade relativa entre eles. Por outras palavras; na firewall real, o "RDP para IIS01" pode ser o número de regra 5, mas, desde é inferior a regra de "Gestão da Firewall" e acima a regra "RDP para DNS01" seria alinhar inteção nesta lista. Também irá ajudar a lista a abaixo cenários que permite uma forma abreviada; Por exemplo "Regra FW 9 (DNS)". Também de uma forma abreviada, as regras RDP quatro será coletivamente chamado, "as regras RDP" quando o cenário de tráfego está relacionado com RDP.

Recuperar também que os grupos de segurança de rede estão no local para o tráfego de internet entrada as sub-redes de front-end e back-end.

#### <a name="allowed-internet-to-web-server"></a>(Permitida) Internet para o servidor Web
1. Página da Internet utilizador pedidos HTTP de SecSvc001.CloudApp.Net (Internet do serviço da nuvem com acesso à)
2. Tráfego de transmite do serviço de nuvem através de pontos finais abertos na porta 80 para a interface de firewall 10.0.0.4:80
3. Nenhum NSG atribuído à sub-rede de segurança, por isso, regras NSG de sistema permitem o tráfego à firewall
4. O tráfego chega a um endereço IP da firewall (10.0.1.4) do
5. Firewall inicia o processamento da regra:
   1. FW Rule 1 (FW Mgmt) não se aplicam, mova a regra seguinte
   2. As regras de FW 2 a 5 (regras de RDP) não se aplicam, mover a regra seguinte
   3. FW regra 6 (aplicação: Web) é aplicável, o tráfego é permitido, os NATs de firewall para 10.0.1.4 (IIS01)
6. A sub-rede do front-end inicia o processamento da regra de entrada:
   1. NSG Rule 1 (bloco de Internet) não se aplica (este tráfego foi NAT iria pela firewall, deste modo, o endereço de origem está agora a firewall que está na sub-rede de segurança e visualizadas pela sub-rede do front-end NSG para ser o tráfego de "local" e, por conseguinte, é permitido), mover a regra seguinte
   2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento de regras do NSG
7. IIS01 está à escuta para o tráfego web, este pedido de recebe e inicia o processamento do pedido
8. Tenta IIS01 inicia uma sessão de FTP para AppVM01 na sub-rede de back-end
9. A rota UDR na sub-rede do front-end faz com que a firewall do próximo salto
10. Não existem regras de saída na sub-rede do front-end, o tráfego é permitido
11. Firewall inicia o processamento da regra:
    1. FW Rule 1 (FW Mgmt) não se aplicam, mova a regra seguinte
    2. FW regra 2-5 (regras de RDP) não se aplicam, mova a regra seguinte
    3. FW regra 6 (aplicação: Web) não se aplicam, mova a regra seguinte
    4. FW regra 7 (aplicação: back-end) é aplicável, o tráfego é permitido, firewall reencaminha o tráfego para 10.0.2.5 (AppVM01)
12. A sub-rede de back-end inicia o processamento da regra de entrada:
    1. NSG Rule 1 (bloco de Internet) não se aplicam, mova a regra seguinte
    2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento de regras do NSG
13. AppVM01 recebe o pedido e inicia a sessão e responde
14. A rota UDR na sub-rede de back-end faz com que a firewall do próximo salto
15. Uma vez que não existe nenhuma saídas regras do NSG na sub-rede de back-end que a resposta é permitida
16. Porque esta está a devolver o tráfego numa sessão estabelecida a firewall passa a resposta de volta para o servidor web (IIS01)
17. Sub-rede de front-end inicia o processamento da regra de entrada:
    1. NSG Rule 1 (bloco de Internet) não se aplicam, mova a regra seguinte
    2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento de regras do NSG
18. O servidor IIS recebe a resposta, conclui a transação com AppVM01 e, em seguida, concluir a criação da resposta HTTP, esta resposta HTTP é enviada para o requerente
19. Uma vez que não existe nenhuma saídas regras do NSG na sub-rede do front-end que a resposta é permitida
20. A resposta HTTP pedidos com êxito a firewall e porque esta é a resposta a uma sessão NAT estabelecida foi aceite pela firewall
21. A firewall redireciona, em seguida, a resposta de volta para o utilizador de Internet
22. Uma vez que existem não saída regras do NSG ou saltos UDR na sub-rede do front-end a resposta é permitida e o utilizador de Internet recebe a página web pedida.

#### <a name="allowed-internet-rdp-to-backend"></a>(Permitida) Internet RDP ao back-end
1. Administrador de servidor na internet pedidos de sessão do RDP para AppVM01 através de SecSvc001.CloudApp.Net:8025, onde 8025 é o número de porta de atribuída ao utilizador para a regra de firewall "RDP para AppVM01"
2. O serviço em nuvem passa o tráfego através de pontos finais abertos na porta 8025 a interface de firewall no 10.0.0.4:8025
3. Nenhum NSG atribuído à sub-rede de segurança, por isso, regras NSG de sistema permitem o tráfego à firewall
4. Firewall inicia o processamento da regra:
   1. FW Rule 1 (FW Mgmt) não se aplicam, mova a regra seguinte
   2. FW regra 2 (RDP IIS) não se aplicam, mova a regra seguinte
   3. FW regra 3 (RDP DNS01) não se aplicam, mova a regra seguinte
   4. Aplicar FW regra 4 (RDP AppVM01), o tráfego é permitido, os NATs de firewall para 10.0.2.5:3386 (porta RDP num AppVM01)
5. A sub-rede de back-end inicia o processamento da regra de entrada:
   1. NSG Rule 1 (bloco de Internet) não se aplica (este tráfego foi NAT iria pela firewall, deste modo, o endereço de origem está agora a firewall que está na sub-rede de segurança e visualizadas pela sub-rede de back-end NSG para ser o tráfego de "local" e, por conseguinte, é permitido), mover a regra seguinte
   2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento de regras do NSG
6. AppVM01 está à escuta para o tráfego RDP e responde
7. Nenhuma saídas regras do NSG, aplicam regras predefinidas e retorno do tráfego é permitido
8. UDR encaminha o tráfego de saída para a firewall como salto seguinte
9. Porque esta está a devolver o tráfego numa sessão estabelecida a firewall passa a resposta de volta para o utilizador de internet
10. Sessão do RDP está ativado
11. AppVM01 solicita a palavra-passe de nome de utilizador

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitida) Pesquisa de DNS do servidor Web num servidor DNS
1. Web Server, IIS01, tem de dados de um feed em www.data.gov, mas tem de resolver o endereço.
2. A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede de back-end) como o servidor DNS primário, IIS01 envia o pedido DNS para DNS01
3. UDR encaminha o tráfego de saída para a firewall como salto seguinte
4. Nenhuma saídas regras NSG estão vinculadas à sub-rede do front-end, o tráfego é permitido
5. Firewall inicia o processamento da regra:
   1. FW Rule 1 (FW Mgmt) não se aplicam, mova a regra seguinte
   2. FW regra 2-5 (regras de RDP) não se aplicam, mova a regra seguinte
   3. FW regras 6 e 7 (regras de aplicação) não se aplicam, mova a regra seguinte
   4. FW regra 8 (para Internet) não se aplicam, mova a regra seguinte
   5. Aplicar a regra de FW 9 (DNS), o tráfego é permitido, firewall reencaminha o tráfego para 10.0.2.4 (DNS01)
6. A sub-rede de back-end inicia o processamento da regra de entrada:
   1. NSG Rule 1 (bloco de Internet) não se aplicam, mova a regra seguinte
   2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento de regras do NSG
7. Servidor DNS recebe o pedido
8. Servidor DNS não tem o endereço em cache e pede-lhe um servidor DNS de raiz na internet
9. UDR encaminha o tráfego de saída para a firewall como salto seguinte
10. Não existem regras de NSG saída numa sub-rede de back-end, o tráfego é permitido
11. Firewall inicia o processamento da regra:
    1. FW Rule 1 (FW Mgmt) não se aplicam, mova a regra seguinte
    2. FW regra 2-5 (regras de RDP) não se aplicam, mova a regra seguinte
    3. FW regras 6 e 7 (regras de aplicação) não se aplicam, mova a regra seguinte
    4. Aplicar a regra de FW 8 (para Internet), o tráfego é permitido, a sessão está a realizar o SNAT enviados ao servidor DNS de raiz na Internet
12. Servidor DNS de Internet responde, uma vez que esta sessão foi iniciada a partir da firewall, a resposta é aceite pela firewall
13. Dado que se trata de uma sessão estabelecida, a firewall reencaminha a resposta para o servidor iniciar, DNS01
14. A sub-rede de back-end inicia o processamento da regra de entrada:
    1. NSG Rule 1 (bloco de Internet) não se aplicam, mova a regra seguinte
    2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento de regras do NSG
15. O servidor DNS recebe e coloca a resposta em cache e, em seguida, responde ao pedido inicial para IIS01
16. A rota UDR na sub-rede de back-end faz com que a firewall do próximo salto
17. Nenhuma saídas regras do NSG existem na sub-rede de back-end, o tráfego é permitido
18. Esta é uma sessão estabelecida na firewall, a resposta é reencaminhada da firewall para o servidor IIS
19. Sub-rede de front-end inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG aplica-se a entrada para a sub-rede do front-end, para que nenhuma do NSG regras aplicam-se o tráfego da sub-rede de back-end
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido
20. IIS01 recebe a resposta do DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Permitida) Servidor de back-end para o servidor de front-end
1. Um administrador com sessão iniciado num AppVM02 através de RDP pede um ficheiro diretamente a partir do servidor IIS01 através do Explorador de ficheiros do windows
2. A rota UDR na sub-rede de back-end faz com que a firewall do próximo salto
3. Uma vez que não existe nenhuma saídas regras do NSG na sub-rede de back-end que a resposta é permitida
4. Firewall inicia o processamento da regra:
   1. FW Rule 1 (FW Mgmt) não se aplicam, mova a regra seguinte
   2. FW regra 2-5 (regras de RDP) não se aplicam, mova a regra seguinte
   3. FW regras 6 e 7 (regras de aplicação) não se aplicam, mova a regra seguinte
   4. FW regra 8 (para Internet) não se aplicam, mova a regra seguinte
   5. Regra de FW 9 (DNS) não se aplicam, mova a seguinte regra
   6. Aplicar a regra de FW 10 (Intra-sub-rede), o tráfego é permitido, firewall transmite o tráfego para 10.0.1.4 (IIS01)
5. Sub-rede de front-end inicia o processamento da regra de entrada:
   1. NSG Rule 1 (bloco de Internet) não se aplicam, mova a regra seguinte
   2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento de regras do NSG
6. Partindo do princípio de autorização e autenticação adequada, IIS01 aceitar o pedido e responde
7. A rota UDR na sub-rede do front-end faz com que a firewall do próximo salto
8. Uma vez que não existe nenhuma saídas regras do NSG na sub-rede do front-end que a resposta é permitida
9. Dado que se trata de uma sessão existente na firewall esta resposta é permitida e a firewall devolve a resposta a AppVM02
10. Sub-rede de back-end inicia o processamento da regra de entrada:
    1. NSG Rule 1 (bloco de Internet) não se aplicam, mova a regra seguinte
    2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento de regras do NSG
11. AppVM02 recebe a resposta

#### <a name="denied-internet-direct-to-web-server"></a>(Negado) Internet direta para o servidor Web
1. Utilizador de Internet tentar aceder ao servidor web, IIS01, através do serviço de FrontEnd001.CloudApp.Net
2. Uma vez que não existem não existem pontos finais abertos para tráfego HTTP, este não seria passar através do serviço de nuvem e não conseguirem contactar o servidor
3. Se os pontos finais tiverem sido abertos por algum motivo, o NSG (bloco de Internet) na sub-rede do front-end bloqueariam este tráfego
4. Por fim, a rota UDR de sub-rede do front-end poderia enviar qualquer tráfego de saída IIS01 para a firewall como salto seguinte e a firewall seria ver estas como tráfego assimétrico e remover a resposta de saída desta forma, existem, pelo menos, três camadas independentes de defesa entre a internet e IIS01 através do seu serviço de nuvem a impedir o acesso não autorizado/inadequada.

#### <a name="denied-internet-to-backend-server"></a>(Negado) Internet para o servidor back-end
1. Utilizador de Internet tenta aceder a um ficheiro no AppVM01 através do serviço de BackEnd001.CloudApp.Net
2. Uma vez que existem não existem pontos finais abertos para partilha de ficheiros, isto não seria com êxito o serviço em nuvem e não conseguirem contactar o servidor
3. Se os pontos finais tiverem sido abertos por algum motivo, o NSG (bloco de Internet) bloqueariam este tráfego
4. Por fim, a rota UDR poderia enviar qualquer tráfego de saída AppVM01 para a firewall como salto seguinte e a firewall seria ver estas como tráfego assimétrico e remover a resposta de saída, por conseguinte, existem, pelo menos, três camadas independentes de defesa entre a internet e AppVM01 através do seu serviço de nuvem a impedir o acesso não autorizado/inadequada.

#### <a name="denied-frontend-server-to-backend-server"></a>(Negado) Servidor front-end para o servidor back-end
1. Assuma IIS01 ficou comprometido e que está a executar código malicioso tentar analisar os servidores de sub-rede de back-end.
2. A rota UDR de sub-rede do front-end enviaria qualquer tráfego de saída do IIS01 à firewall como salto seguinte. Não é um problema que pode ser alterada pela VM comprometida.
3. Se o pedido foi para AppVM01 ou para o servidor DNS para pesquisas DNS que tráfego foi potencialmente ser permitido pela firewall (devido a FW regras 7 e 9), a firewall seria processar o tráfego. Todos os outros tráfegos seriam bloqueado por FW regra 11 (negar todos).
4. Se a deteção de ameaças de avançadas foi ativado na firewall (que não é abrangida neste documento, consulte a documentação do fabricante do seu dispositivo de rede específico ameaça capacidades avançadas), mesmo tráfego teria permitido pelas regras de reencaminhamento básico abordado neste documento foi impedido se o tráfego contidos conhecido em assinaturas ou padrões sinalizador uma regra de ameaça avançadas.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negado) Pesquisa de DNS de Internet no servidor DNS
1. Utilizador de Internet tenta pesquisar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net 
2. Uma vez que não existem não existem pontos finais aberta para o tráfego DNS, este não seria passar através do serviço de nuvem e não conseguirem contactar o servidor
3. Se os pontos finais tiverem sido abertos por algum motivo, a regra NSG (bloco de Internet) na sub-rede do front-end bloqueariam este tráfego
4. Por fim, a rota UDR de sub-rede de back-end poderia enviar qualquer tráfego de saída DNS01 para a firewall como salto seguinte e a firewall seria ver estas como tráfego assimétrico e remover a resposta de saída desta forma, existem, pelo menos, três camadas independentes de defesa entre o Internet e DNS01 através do seu serviço de nuvem a impedir o acesso não autorizado/inadequada.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negado) Internet para o acesso através da Firewall do SQL Server
1. Internet utilizador solicita dados SQL SecSvc001.CloudApp.Net (Internet do serviço da nuvem com acesso à)
2. Uma vez que não existem não existem pontos finais aberto para o SQL Server, este não seria com êxito o serviço em nuvem e não alcançar a firewall
3. Se os pontos finais do SQL tiverem sido abertos por algum motivo, a firewall seria começar processamento da regra:
   1. FW Rule 1 (FW Mgmt) não se aplicam, mova a regra seguinte
   2. As regras de FW 2 a 5 (regras de RDP) não se aplicam, mover a regra seguinte
   3. FW regra 6 e 7 (regras de aplicação) não se aplicam, mova a regra seguinte
   4. FW regra 8 (para Internet) não se aplicam, mova a regra seguinte
   5. Regra de FW 9 (DNS) não se aplicam, mova a seguinte regra
   6. FW regra 10 (Intra-sub-rede) não se aplicam, mova a regra seguinte
   7. Aplicar FW regra 11 (negar todos), o tráfego é o processamento da regra bloqueadas, parar

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e a configuração de rede
Guarde o Script completo num ficheiro de script do PowerShell. Guarde a configuração de rede para um ficheiro com o nome "NetworkConf2.xml".
Modificar as variáveis definidas pelo utilizador, conforme necessário. Execute o script e, em seguida, siga as instruções de configuração da regra de Firewall acima.

#### <a name="full-script"></a>Script completa
Este script irão, com base nas variáveis definidas pelo utilizador:

1. Ligar a uma subscrição do Azure
2. Criar uma nova conta de armazenamento
3. Criar uma nova VNet e três sub-redes, tal como definido no ficheiro de configuração de rede
4. Criar máquinas virtuais cinco; 1 firewall e servidor 4 windows VMs
5. Configure UDR, incluindo:
   1. Criar duas novas tabelas de rota
   2. Adicionar rotas às tabelas
   3. Vincular tabelas a sub-redes adequadas
6. Ativar o reencaminhamento IP na NVA
7. Configure o NSG, incluindo:
   1. Criar um NSG
   2. Adicionar uma regra
   3. Enlace o NSG para as sub-redes adequadas

Este script do PowerShell deve ser executado localmente num que Internet ligado PC ou servidor.

> [!IMPORTANT]
> Quando este script é executado, pode haver avisos ou outras mensagens informativas destaque no PowerShell. Apenas mensagens de erro vermelho são a causa possível para preocupação.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Ficheiro de configuração de rede
Guarde este ficheiro xml com localização atualizada e adicionar a ligação a este ficheiro para a variável de $NetworkConfigFile no script acima.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Scripts de aplicação de exemplo
Se pretender instalar uma aplicação de exemplo para isto e outros exemplos de rede de Perímetro, um foi fornecido na seguinte hiperligação: [Script de aplicação de exemplo][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Rede de Perímetro bidirecional com NVA, NSG e UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Vista lógica das regras de Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Criar um objeto de rede de front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Criar um objeto de servidor DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Cópia de regra predefinida do RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regra de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ícone de redirecionamento da aplicação"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ícone NAT de destino"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Ícone de passagem"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regra de firewall de gestão"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regra de RDP de firewall"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regra de firewall Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regra de AppVM01 de firewall"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regra de saída de firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regra de firewall DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regra de VNet dentro da firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regra de negação de firewall"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Ativação da regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
