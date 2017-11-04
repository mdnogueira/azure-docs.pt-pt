---
title: "Infraestrutura e de conectividade para SAP HANA no Azure (instâncias de grande) | Microsoft Docs"
description: "Configure a infraestrutura de conectividade necessários para utilizar o SAP HANA no Azure (instâncias de grande)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7a44fdbfb973d75c21aa87e9b9d0eea8fb2b3392
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infraestrutura de SAP HANA (instâncias de grandes dimensões) e a conectividade no Azure 

Algumas definições de antemão antes de ler este guia. No [descrição geral de SAP HANA (instâncias de grandes dimensões) e arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) introduzimos duas classes diferentes de unidades de instância grande HANA com:

- S72, S72m, S144, S144m, S192 e S192m, que denominamos 'Type posso de classe' de SKUs.
- S384, S384m, S384xm, S576, S768 e S960, que denominamos 'class II de tipo' de SKUs.

Os especificadores de classe que vão ser utilizados em toda a documentação de instância grande HANA eventualmente referir-se a funcionalidades diferentes e requisitos com base nas HANA grande SKUs de instância.

Outras definições utilizadas frequentemente são:
- **Carimbo de instância grande:** uma pilha de infraestrutura de hardware que é o SAP HANA TDI certificada e dedicados para executar instâncias de SAP HANA no Azure.
- **SAP HANA no Azure (instâncias de grande):** oficial nome para a oferta no Azure executar HANA instâncias no SAP HANA TDI certificadas hardware que é implementada em carimbos de data / instância grande em diferentes regiões do Azure. O termo relacionado **instância grande HANA** é curto para SAP HANA no Azure (instâncias de grandes dimensões) e é amplamente utilizado neste guia de implementação técnica.
 

Após a compra de SAP HANA no Azure (instâncias de grande) está finalizada entre si e a equipa da conta Microsoft enterprise, as seguintes informações é necessário pela Microsoft para implementar unidades de instância grande HANA:

- Nome do cliente
- Informações de contactos empresariais (incluindo o endereço de correio eletrónico e o número de telefone)
- Informações de contacto técnicas (incluindo o número de telefone e endereço de correio eletrónico)
- Informações de contactos rede técnicas (incluindo o número de telefone e endereço de correio eletrónico)
- Região de implementação do Azure (EUA oeste, EUA leste, leste da Austrália, Sudeste da Austrália, Europa Ocidental e Europa do Norte a partir de Julho de 2017)
- Confirmar SAP HANA no Azure (instâncias de grande) SKU (configuração)
- Como já detalhadas no documento de descrição geral e arquitetura para instâncias de grande HANA, para cada região do Azure que está a ser implementada:
    - / 29 intervalo de endereços IP para ligações de ER P2P que ligar VNets do Azure para instâncias de grande HANA
    - Um /24 bloco CIDR utilizados para o conjunto IP do HANA grande instâncias de servidor
- Os valores de intervalo de endereços IP utilizados no atributo de espaço de endereços VNet de cada VNet do Azure que liga as instâncias de grande HANA
- Dados para cada sistema de instâncias de grande HANA:
  - Nome de anfitrião desejado - Idealmente com o nome de domínio completamente qualificado.
  - Endereço IP pretendido para a unidade de instância grande HANA fora do intervalo de endereços do conjunto de IP do servidor - tenha em atenção que os 30 primeiros endereços IP no intervalo de endereços de conjunto de IP do servidor estão reservados para utilização interna dentro de instâncias de grande HANA
  - Nome de SAP HANA SID para a instância de SAP HANA (necessário para criar os volumes de disco relacionados com o SAP HANA necessário). O SID de HANA é necessário para criar as permissões para <sidadm> nos volumes de NFS, qual obter anexados à unidade de instância grande HANA. Também é utilizado como um dos componentes nome dos volumes de disco que obterem montados. Se pretender executar mais do que uma instância HANA na unidade, terá de lista várias HANA SIDs. Cada um deles obtém um conjunto separado dos volumes atribuídos.
  - O groupid que tem do utilizador hana sidadm no SO Linux é necessário para criar os volumes de disco necessários relacionados com o SAP HANA. A instalação de SAP HANA normalmente cria o grupo de sapsys com um id de grupo de 1001. O utilizador hana sidadm faz parte desse grupo
  - O ID de utilizador que tem do utilizador hana sidadm no SO Linux é necessário para criar os volumes de disco necessários relacionados com o SAP HANA. Se estiver a executar várias instâncias HANA na unidade, tem de listar todas as o <sid>adm utilizadores 
- ID de subscrição do Azure para a subscrição do Azure para que SAP HANA no Azure HANA instâncias grande que vão ser diretamente ligado. Este ID de subscrição referencia a subscrição do Azure, que será debitado no valor as ou as unidades de instância grande HANA.

Depois de fornecer as informações, a Microsoft Aprovisiona SAP HANA no Azure (instâncias de grandes dimensões) e irá devolver as informações necessárias para ligar as VNets do Azure para instâncias de grande HANA e para as unidades de instância grande HANA de acesso.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Ligar as VMs do Azure para instâncias de grande HANA

Como já mencionados [descrição geral de SAP HANA (instâncias de grandes dimensões) e arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) como a implementação mínima de instâncias de grande HANA com a camada da aplicação SAP na procura do Azure:

![VNet do Azure ligado ao SAP HANA no Azure (instâncias de grandes dimensões) e no local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Procura quanto mais próximo no lado de VNet do Azure, mas tenha em consideração a necessidade de:

- A definição de uma VNet do Azure que vai ser utilizado para implementar as máquinas virtuais da camada de aplicação SAP para.
- Que automaticamente significa que uma sub-rede de predefinição na Vnet do Azure é definido que é utilizada para implementar as VMs em.
- A VNet do Azure que é criado tem de ter pelo menos uma sub-rede VM e uma sub-rede de Gateway do ExpressRoute. Estas sub-redes devem ser atribuídas os intervalos de endereços IP especificado e referido nas secções seguintes.

Por isso, vamos ver um pouco mais próximo para a criação da VNet do Azure para instâncias de grande HANA

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Criação da VNet do Azure para instâncias de grande HANA

>[!Note]
>A VNet do Azure para a instância de grande HANA deve ser criada utilizando o modelo de implementação Azure Resource Manager. O modelo de implementação do Azure mais antigo, habitualmente conhecido como modelo de implementação clássica, não é suportado com a solução de instância grande HANA.

A VNet pode ser criada utilizando o portal do Azure, o PowerShell, o modelo do Azure ou a CLI do Azure (consulte [criar uma rede virtual com o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). No exemplo seguinte, vamos ver para uma VNet criada através do portal do Azure.

Se vamos ver para as definições de uma VNet do Azure através do portal do Azure, vamos ver para algumas das definições e os a inter-relação iremos lista intervalos de endereços IP diferentes. Como podemos são se fala sobre o **espaço de endereços**, significa o espaço de endereço que a VNet do Azure está autorizada a utilizar. Este espaço de endereços também é o intervalo de endereços que utiliza a VNet para propagação de rota BGP. Isto **espaço de endereços** podem ser vistos aqui:

![Endereço espaço da VNet do Azure apresentado no portal do Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No caso de precedente com 10.16.0.0/16, a VNet do Azure foi indicada um intervalo de endereços IP em vez disso grande e wide a utilizar. Significa que todos os intervalos de endereços IP de sub-redes subsequentes dentro desta VNet podem ter os respetivos intervalos dentro desse 'espaço de endereços'. Normalmente, não recomendamos um intervalo de endereços de grandes dimensões para VNet único no Azure. Mas obter um passo adicional, vamos ver em sub-redes definidas na VNet do Azure:

![Intervalos de endereços de sub-redes de VNet do Azure e os respetivos IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Como pode ver, vamos ver uma VNet com uma sub-rede VM primeiro (aqui denominado 'default') e uma sub-rede denominada "GatewaySubnet".
A secção seguinte, iremos referem-se ao intervalo de endereços IP da sub-rede, que foi chamado 'default' de gráficos como **intervalo de endereços IP de sub-rede VM do Azure**. As secções seguintes, vamos referem-se ao intervalo de endereços IP da sub-rede de Gateway como **intervalo de endereços IP de sub-rede de Gateway de VNet**. 

No caso demonstrado através de dois gráficos acima, verá que o **espaço de endereços VNet** abranja ambos, o **intervalo de endereços IP de sub-rede VM do Azure** e **intervalo de endereços IP de sub-rede de Gateway de VNet**. 

Noutros casos em que tem de conservar ou ser específico com os intervalos de endereços IP, pode restringir a **espaço de endereços VNet** de uma VNet para intervalos específicos que está a ser utilizado por cada sub-rede. Neste caso, pode definir o **espaço de endereços VNet** de uma VNet específico como vários intervalos conforme mostrado aqui:

![Espaço de endereços VNet do Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Neste caso, o **espaço de endereços VNet** tem dois espaços definidos. Estes dois espaços, são idênticos aos intervalos de endereços IP definidos para **intervalo de endereços IP de sub-rede VM do Azure** e **intervalo de endereços IP de sub-rede de Gateway de VNet.**

Pode utilizar qualquer padrão de nomenclatura que quiser para estas sub-redes de inquilino (sub-redes VM). No entanto, **tem sempre de existir um e apenas uma sub-rede do gateway para cada VNet** que estabelece ligação ao SAP HANA, no circuito de ExpressRoute do Azure (instâncias de grande). E **esta sub-rede de gateway deve sempre ser com o nome "GatewaySubnet"** para garantir a colocação adequada do ExpressRoute gateway.

> [!WARNING] 
> É essencial que a sub-rede do gateway sempre é com o nome "GatewaySubnet".

Várias sub-redes VM podem ser utilizadas, mesmo a utilização de intervalos de endereços não contíguo. Mas como mencionado anteriormente, estes intervalos de endereços tem de ser abrangidos pelo **espaço de endereços VNet** da VNet de forma agregada ou numa lista de intervalos de exatos de sub-redes VM e a sub-rede do gateway.

Resumir o factos importantes sobre uma VNet do Azure que se liga a instâncias de grande HANA:

- Tem de submeter para a Microsoft a **espaço de endereços VNet** quando efetuar uma implementação inicial de instâncias de grande HANA. 
- O **espaço de endereços VNet** pode ser um intervalo maior que abrange o intervalo para intervalos de endereços IP de sub-rede VM do Azure e o intervalo de endereços IP de sub-rede de Gateway de VNet.
- Ou pode submeter como **espaço de endereços VNet** vários intervalos que abrangem o IP de outro endereço de intervalos de intervalos de endereços IP de sub-rede VM e o intervalo de endereços IP de sub-rede de Gateway de VNet.
- O definido **espaço de endereços VNet** é utilizada propagação de encaminhamento de BGP.
- O nome da sub-rede do Gateway tem de ser: **"GatewaySubnet".**
- O **espaço de endereços VNet** é utilizado como um filtro no lado instância grande HANA para permitir ou não permitir o tráfego para as unidades de instância grande HANA a partir do Azure. Se as informações de encaminhamento de BGP da VNet do Azure e os intervalos de endereços IP configurados para filtragem do lado do instância grande HANA não corresponderem, podem surgir problemas na conectividade.
- Existem alguns detalhes sobre a sub-rede do Gateway que são abordados mais baixo na secção 'Ligar uma VNet HANA grande instância expressroute'



### <a name="different-ip-address-ranges-to-be-defined"></a>Intervalos de endereços IP diferentes definido 

Introduzimos já algumas dos intervalos de endereços IP necessários para implementar instâncias de grande HANA nas secções anteriores. Mas existem algumas mais intervalos de endereços IP, o que são importantes. Vamos seguir alguns detalhes adicionais. Os seguintes endereços IP que nem todos os tem de ser submetido para a Microsoft tem de ser definida antes de enviar um pedido para a implementação inicial:

- **Espaço de endereços da VNet:** como já introduzidas anteriormente, ou o endereço range(s) atribuiu IP (ou estiver a planear para atribuir) para o parâmetro de espaço de endereços na ligação de rede Virtual do Azure (VNet) para o ambiente de instância de grandes dimensões do SAP HANA. Recomenda-se que este parâmetro de espaço de endereços é um valor de com várias linha composto de intervalos de sub-rede VM do Azure e o intervalo de sub-rede de Gateway do Azure, conforme mostrado anteriormente de gráficos. Este intervalo não pode sobrepor com o local ou com intervalos de endereços do conjunto de IP do servidor ou ER P2P. Como obter esta ou estes intervalos de endereços IP? O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um ou vários intervalos de endereços de IP, que é ou não são utilizadas dentro da sua rede. Exemplo: Se a sub-rede VM do Azure (consulte anteriormente) 10.0.1.0/24, e a sub-rede de Gateway do Azure (consulte a seguir) é 10.0.2.0/28, em seguida, o espaço de endereços VNet do Azure é recomendado para ser duas linhas; 10.0.1.0/24 e 10.0.2.0/28. Embora os valores de espaço de endereços podem ser agregados, recomenda-se correspondência destes com a intervalos de sub-rede para evitar reutilização acidental não utilizados intervalos de endereços IP dentro de maior espaços de endereços no futuro noutro local na sua rede. **O espaço de endereços VNET é um intervalo de endereços IP que tem de ser submetido para a Microsoft quando solicitar uma implementação inicial**

- **Azure intervalo de endereços IP a sub-rede VM:** intervalo de endereços IP este tal como explicado anteriormente já, é aquele que atribuiu (ou se pretende atribuir) para o parâmetro de sub-rede da VNet do Azure no seu Azure VNET estabelecer ligação ao ambiente de SAP HANA grande instância. Este intervalo de endereços IP é utilizado para atribuir endereços IP para as VMs do Azure. Os endereços IP fora deste intervalo estão autorizados a ligar para os servidores de instância de grandes dimensões do SAP HANA. Se for necessário, podem ser utilizadas várias sub-redes VM do Azure. A/24 bloco CIDR é recomendada pela Microsoft para cada sub-rede de VM do Azure. Este intervalo de endereços tem de ser uma parte os valores utilizados no espaço de endereços VNet do Azure. Como obter este intervalo de endereços IP? O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um intervalo de endereços IP, que não esteja atualmente utilizado no interior da rede.

- **Intervalo de endereços IP de sub-rede de Gateway de VNet:** consoante as funcionalidades que pretende utilizar, o tamanho recomendado seria:
   - Gateway do ExpressRoute de desempenho Ultra: / 26 bloco de endereços - necessário para a classe de tipo II de SKUs
   - Coexistência com o ExpressRoute utilizando um Gateway do ExpressRoute de elevado desempenho e de VPN (ou mais pequenos): / 27 bloco de endereços
   - Todas as outras situações: / 28 bloco de endereços. Este intervalo de endereços tem de ser uma parte os valores utilizados os valores de "Espaço de endereços da VNet". Este intervalo de endereços tem de ser uma parte os valores utilizados os valores de espaço de endereços VNet do Azure que precisa para submeter a Microsoft. Como obter este intervalo de endereços IP? O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um intervalo de endereços IP, que não esteja atualmente utilizado no interior da rede. 

- **Endereço do intervalo para a conectividade de ER P2P:** este intervalo é o intervalo IP para a sua ligação de SAP HANA grande instância ExpressRoute (ER) P2P. Este intervalo de endereços IP tem de ser/29 intervalo de endereços de CIDR IP. Este intervalo não pode sobrepor com seu no local ou outras IP Azure intervalos de endereços. Este intervalo de endereços IP é utilizado para configurar a conectividade de ER do Gateway do ExpressRoute de VNet do Azure para os servidores de instância de grandes dimensões do SAP HANA. Como obter este intervalo de endereços IP? O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um intervalo de endereços IP, que não esteja atualmente utilizado no interior da rede. **Este intervalo é de um intervalo de endereços IP que tem de ser submetido para a Microsoft quando solicitar uma implementação inicial**
  
- **Intervalo de endereços do conjunto IP do servidor:** este intervalo de endereços IP é utilizado para atribuir o endereço IP individual para servidores de grande instância HANA. O tamanho da sub-rede recomendada é um /24 CIDR bloquear - Contudo, se necessário, pode ser inferior a um mínimo de fornecer 64 endereços IP. Este intervalo de 30 primeiros endereços IP reservados para utilização pela Microsoft. Certifique-se de que este facto é tida em consideração quando escolher o tamanho do intervalo. Este intervalo não pode sobrepor com seu no local ou outras IP Azure endereços. Como obter este intervalo de endereços IP? O fornecedor de equipa ou o serviço de rede empresarial deve fornecer um intervalo de endereços IP que não esteja atualmente utilizado no interior da rede. Um /24 (recomendado) CIDR exclusivo bloquear a ser utilizada para atribuir os endereços IP específicos, necessários para SAP HANA no Azure (instâncias de grande). **Este intervalo é de um intervalo de endereços IP que tem de ser submetido para a Microsoft quando solicitar uma implementação inicial**
 
Se precisar de definir e planear os intervalos de endereços IP acima, nem todos os mesmos têm de ser transmitidas à Microsoft. Para resumir acima, os intervalos de endereços IP que é necessários para o nome para a Microsoft são:

- Endereço de VNet do Azure Space(s)
- Intervalo de endereços de conetividade de ER P2P
- Intervalo de endereços do conjunto IP do servidor

Adicionar VNets adicionais que necessitam para ligar a instâncias de grande HANA, requer a submeter o novo espaço de endereços de VNet do Azure que estiver a adicionar à Microsoft. 

Segue-se um exemplo de alguns intervalos de exemplo e os intervalos diferentes conforme necessário configurar e, eventualmente, conceder à Microsoft. Como pode ver, o valor para o espaço de endereços VNet do Azure não é agregado no primeiro exemplo, mas não está definido de intervalos de intervalo de endereços IP de sub-rede a primeira VM do Azure e o intervalo de endereços IP de sub-rede de Gateway de VNet. Utilizar várias sub-redes VM dentro da VNet do Azure funciona em conformidade pela configuração e submeter o IP adicional intervalos de endereços de subnet(s) VM adicionais como parte do espaço de endereços VNet do Azure.

![Intervalos de endereços IP necessários na SAP HANA na implementação mínima do Azure (instâncias de grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Também tem a possibilidade de agregar os dados que submeter para a Microsoft. Nesse caso, o espaço de endereços da VNet do Azure apenas inclui um espaço. Utilizando os intervalos de endereços IP utilizados no exemplo anterior. Este espaço de endereço VNet agregado pode ter o seguinte aspeto:

![Segundo possibilidade de intervalos de endereços IP necessários na SAP HANA na implementação mínima do Azure (instâncias de grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Como pode ver acima, em vez de dois intervalos menores que definido o espaço de endereços da VNet do Azure, temos um intervalo maior que abrange 4096 endereços IP. Uma definição grande do espaço de endereços deixa algumas em vez disso, grande intervalos não utilizados. Uma vez que os valores de espaço de endereços VNet são utilizados para propagação de rota BGP, utilização de intervalos de não utilizados no local ou noutro local na sua rede pode causar problemas de encaminhamento. Por isso, é recomendado para manter o espaço de endereços estreitamente alinhado com o espaço de endereços de sub-rede real utilizado. Se for necessário, sem incorrer em períodos de indisponibilidade na VNet, pode sempre adicionar a valores de espaço de endereços novo mais tarde.
 
> [!IMPORTANT] 
> Cada endereço IP tem de espaço de endereços do intervalo de ER-P2P, conjunto de IP do servidor, do Azure VNet **não** sobrepor-se entre si ou qualquer outro intervalo utilizado em qualquer outro na sua rede local; têm de ser discreta e como os dois gráficos Mostrar anterior, não pode ser um sub-rede de qualquer outro intervalo. Se ocorrem sobreposições entre os intervalos, a VNet do Azure pode não estabelecer ligação ao circuito ExpressRoute.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Passos seguintes depois de terem sido definidos intervalos de endereços
Depois de terem sido definidos os intervalos de endereços IP, tem de acontecer seguintes atividades:

1. Submeta os intervalos de endereços IP para o espaço de endereços VNet do Azure, a conectividade de ER P2P e o intervalo de endereços do conjunto de IP do servidor, juntamente com outros dados que tenham sido listados no início do documento. Neste ponto no tempo, também pode começar a criar a VNet e as sub-redes VM. 
2. Um circuito Expressroute é criado pela Microsoft entre a sua subscrição do Azure e o carimbo de instância grande HANA.
3. Uma rede de inquilino é criada no carimbo instância grande pela Microsoft.
4. Microsoft configura o funcionamento em rede no SAP HANA na infraestrutura do Azure (instâncias de grande) para aceitar endereços IP do seu Azure VNet espaço de endereços que comunica com instâncias de grande HANA.
5. Consoante o específico SAP HANA no SKU de (instâncias de grande) do Azure adquirido, Microsoft atribui-lhe uma unidade de computação numa rede de inquilino, alocar armazenamento de montagem e instalar o sistema operativo (SUSE ou Red Hat Linux). Endereços IP para estas unidades são retirados o endereço de conjunto de IP do servidor intervalo tiver submetido para a Microsoft.

No final do processo de implementação, a Microsoft fornece os seguintes dados para si:
- Informações necessárias para estabelecer ligação a VNet(s) do Azure para o circuito de ExpressRoute que liga as VNets do Azure para instâncias de grande HANA:
     - Chaves de autorização
     - PeerID do ExpressRoute
- Dados aceder instâncias grande HANA depois de estabelecer o circuito do ExpressRoute e a VNet do Azure.

Também pode encontrar a sequência de ligar a instâncias de grande HANA no documento [configuração ponto a ponto para SAP HANA grande instâncias](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Muitos dos seguintes passos são apresentados num exemplo de implementação contidas nesse documento. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Ligar uma VNet HANA instância grande expressroute

À medida que definidos todos os intervalos de endereços IP e agora obteve novamente os dados da Microsoft, pode começar a ligar a VNet criada antes da instâncias de grande HANA. Assim que for criada a VNet do Azure, um gateway ExpressRoute tem de ser criado a vnet para ligar a VNet para o circuito de ExpressRoute que liga ao inquilino ao cliente no carimbo instância grandes.

> [!NOTE] 
> Este passo pode demorar até 30 minutos a concluir, como o novo gateway é criado na subscrição do Azure designada e, em seguida, ligado para a VNet do Azure especificada.

Se já existe um gateway, verifique se é um gateway ExpressRoute ou não. Caso contrário, o gateway tem de ser eliminado e recriado como um gateway do ExpressRoute. Se um gateway ExpressRoute já for estabelecido, uma vez que a VNet do Azure já está ligada ao circuito ExpressRoute para conectividade no local, avance para a secção de ligar VNets abaixo.

- Utilizar qualquer um da (novo) [portal do Azure](https://portal.azure.com/), ou o PowerShell para criar um gateway de VPN do ExpressRoute estiver ligado à sua VNet.
  - Se utilizar o portal do Azure, adicione um novo **Gateway de rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se tiver escolhido o PowerShell, em vez disso, transfira o primeiro e utilizar a versão mais recente [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) para garantir uma experiência ideal. Os comandos seguintes criam um gateway do ExpressRoute. Os textos precedido por um  _$_  são variáveis definidas pelo utilizador que necessitam de ser atualizados com as suas informações específicas.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, o SKU de gateway HighPerformance foi utilizado. As opções são HighPerformance ou UltraPerformance como o gateway só SKUs que são suportados para SAP HANA no Azure (instâncias de grande).

> [!IMPORTANT]
> Para tipos de instâncias de grande HANA do SKU S384, S384m, S384xm, S576, S768 e S960 (SKUs de classe de tipo II), a utilização do SKU de Gateway UltraPerformance é obrigatória.

### <a name="linking-vnets"></a>Ligar VNets

Agora que a VNet do Azure tem um gateway ExpressRoute, utilize as informações de autorização fornecidas pela Microsoft para ligar o gateway do ExpressRoute para o SAP HANA no circuito de ExpressRoute do Azure (instâncias de grande) criado para esta conetividade. Este passo pode ser efetuado utilizando o portal do Azure ou o PowerShell. O portal é recomendado, no entanto instruções do PowerShell são os seguintes. 

- Execute os seguintes comandos para cada gateway de VNet utilizando um AuthGUID diferente para cada ligação. As primeiras duas entradas mostradas a seguir script provenientes as informações fornecidas pela Microsoft. Além disso, o AuthGUID é específica para cada VNet e o gateway. Significa que, se pretender adicionar outra VNet do Azure, terá de obter AuthID outro para o seu circuito de ExpressRoute liga instâncias grande HANA no Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Se pretender ligar o gateway para vários circuitos ExpressRoute que estão associados a sua subscrição, poderá ter de executar este passo mais do que uma vez. Por exemplo, provavelmente, vai ligar o mesmo Gateway de VNet para o circuito de ExpressRoute que se liga a VNet à sua rede no local.

## <a name="adding-more-ip-addresses-or-subnets"></a>Adicionar mais sub-redes ou endereços IP

Utilize o portal do Azure, PowerShell ou CLI adicionar mais IP endereços ou sub-redes.

Neste caso, a recomendação está a adicionar o novo intervalo de endereços IP como novo intervalo para o espaço de endereços da VNet em vez de gerar um novo intervalo agregado. Em ambos os casos, tem de submeter esta alteração para a Microsoft para permitir a conetividade fora desse intervalo de endereços IP novo para as unidades de instância grande HANA no seu cliente. Pode abrir um pedido de suporte do Azure para obter o novo espaço de endereço VNet adicionado. Depois de receber a confirmação, execute os passos seguintes.

Para criar uma sub-rede adicional a partir do portal do Azure, consulte o artigo [criar uma rede virtual com o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)e para criar a partir do PowerShell, consulte [criar uma rede virtual com o PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Adicionar VNets

Depois de ligar inicialmente um ou mais VNets do Azure, pode querer adicionar aqueles adicionais que acedem ao SAP HANA no Azure (instâncias de grande). Em primeiro lugar, submeta um pedido de suporte do Azure, esse pedido incluem ambas as informações específicas que identifica a implementação do Azure específica e os intervalos de espaço de endereços IP do espaço de endereços VNet do Azure. SAP HANA na gestão de serviço do Azure, em seguida, fornece as informações necessárias para ligar o ExpressRoute e as VNets adicionais. Para cada VNet, precisa de uma chave exclusiva de autorização para estabelecer ligação ao circuito ExpressRoute para instâncias de grande HANA.

Passos para adicionar uma nova VNet do Azure:

1. Completa do primeiro passo no processo de integração, consulte o _criar Azure VNet_ secção.
2. Completa do segundo passo no processo de integração, consulte o _criar a sub-rede do gateway_ secção.
3. Para ligar as suas VNets adicionais para o circuito de ExpressRoute de instância grande HANA, abra um pedido de suporte do Azure com informações sobre a nova VNet e uma nova chave de autorização do pedido.
4. Depois de notificado de que a autorização está concluída, utilize as informações de autorização fornecidos Microsoft para concluir o terceiro passo no processo de integração, consulte o _ligar VNets_ secção.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Aumentar a largura de banda de circuito de ExpressRoute

Consulte o SAP HANA na gestão de serviço do Azure. Se que aconselhado para aumentar a largura de banda de SAP HANA, no circuito de ExpressRoute do Azure (instâncias de grande), crie um pedido de suporte do Azure. (Pode pedir um aumento de uma largura de banda do circuito único até um máximo de 10 Gbps.) Em seguida, receber notificação depois da operação foi concluída; nenhuma ação adicional necessária para ativar esta velocidade superior no Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>A adição de um circuito de ExpressRoute adicional

Consulte o SAP HANA na gestão de serviço do Azure, se aconselha-se que é necessário um circuito de ExpressRoute adicional, efetuar do Azure suporta a pedido para criar um circuito de ExpressRoute novo (e para obter informações de autorização para ligar ao mesmo). O espaço de endereços que vai ser utilizado nas VNets tem de ser definidas antes de efetuar o pedido, por ordem de SAP HANA na gestão de serviço do Azure para fornecer autorização.

Assim que o circuito nova é criado e o SAP HANA na configuração de gestão de serviço do Azure está concluída, vai receber notificação com as informações que precisa continuar. Siga os passos descritos acima para criar e ligar a nova VNet para este circuito adicional. Não é possível ligar VNets do Azure para este circuito adicional se já ligado a outro SAP HANA no circuito de ExpressRoute do Azure (instância grande) na mesma região do Azure.

## <a name="deleting-a-subnet"></a>Eliminar uma sub-rede

Para remover uma sub-rede da VNet, pode ser utilizado ou o portal do Azure, PowerShell ou CLI. No caso o seu Azure VNet IP endereço intervalo/Azure espaço de endereços VNet era um intervalo de agregados, não há nenhum siga para a com a Microsoft. Exceto que a VNet é ainda propagar espaço de endereços de rota BGP que inclui a sub-rede eliminada. Se definido o IP de VNet do Azure endereço intervalo/Azure espaço de endereços VNet como vários intervalos de endereços IP que um foi atribuído à sua sub-rede eliminado, deve eliminar que fora do seu espaço de endereços VNet e, subsequentemente, informar SAP HANA na gestão de serviço do Azure para removê-lo de intervalos de SAP HANA no Azure (instâncias de grande) está autorizado a comunicar com.

Enquanto existe ainda não está específico, dedicado Azure.com obter orientações sobre como remover sub-redes, o processo de remoção de sub-redes é a reversão do processo para adicioná-las. Consulte o artigo [criar uma rede virtual com o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre a criação de sub-redes.

## <a name="deleting-a-vnet"></a>Eliminar uma VNet

Utilize o portal do Azure, PowerShell ou CLI aquando da eliminação de uma VNet. SAP HANA na gestão de serviço do Azure remove as autorizações existentes no SAP HANA no circuito de ExpressRoute do Azure (instâncias de grandes dimensões) e remova o IP de VNet do Azure endereço intervalo/Azure espaço de endereços VNet para a comunicação com instâncias de grande HANA.

Assim que tiver sido removida a VNet, abra um pedido de suporte do Azure para fornecer os intervalos de espaço de endereços IP a serem removidos.

Enquanto existe ainda não está específico, dedicado Azure.com obter orientações sobre como remover VNets, o processo para remover as VNets é a reversão do processo para adicioná-los, que é descrito acima. Consulte os artigos [criar uma rede virtual com o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [criar uma rede virtual com o PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre a criação de VNets.

Certifique-se de que tudo está a ser removido, elimine os seguintes itens:

- A ligação do ExpressRoute, Gateway de VNet, IP público do Gateway de VNet e VNet

## <a name="deleting-an-expressroute-circuit"></a>Eliminar um circuito do ExpressRoute

Para remover um SAP HANA adicionais no circuito de ExpressRoute do Azure (instâncias de grande), abra um pedido de suporte do Azure com SAP HANA na gestão de serviço do Azure e solicitar que o circuito deve ser eliminado. Dentro da subscrição do Azure, pode eliminar ou manter a VNet conforme necessário. No entanto, tem de eliminar a ligação entre o circuito de ExpressRoute de instâncias de grande HANA e o gateway de VNet ligado.

Se também pretender remover uma VNet, siga as orientações sobre como eliminar uma VNet na secção acima.


