---
title: "Práticas recomendadas de segurança de rede do Azure | Microsoft Docs"
description: "Saiba mais algumas das principais funcionalidades disponíveis no Azure para o ajudar a criar ambientes de rede segura"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: fb5e399d4ab02a7f2805cc280b213bf5b44f6993
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-cloud-services-and-network-security"></a>Segurança de rede e serviços da nuvem Microsoft
Serviços cloud Microsoft fornecem serviços de hiper escala e infraestrutura, as capacidades de nível empresarial e muitas opções para conectividade híbrida. Os clientes podem optar por aceder a esses serviços através da Internet ou com o ExpressRoute do Azure, que fornece a conectividade de rede privada. A plataforma Microsoft Azure permite aos clientes expandir a sua infraestrutura para a nuvem e criar arquiteturas de várias camadas de forma totalmente integrada. Além disso, terceiros pode ativar avançadas de capacidades por oferta de serviços de segurança e de aplicações virtuais. Este documento técnico fornece uma descrição geral da arquitetura problemas que os clientes devem ter quando o Microsoft cloud services acedidos através do ExpressRoute e de segurança. Também abrange a criação de serviços mais seguros em redes virtuais do Azure.

## <a name="fast-start"></a>Início rápido
O gráfico de lógica seguinte pode direcioná-lo para um exemplo de específico de várias técnicas de segurança disponíveis com a plataforma do Azure. Para referência rápida, localize o exemplo que melhor se adequa à sua maiúsculas e minúsculas. Para explicações expandidas, continue a ler através do documento.
[![0]][0]

[Exemplo 1: Crie uma rede de perímetro (também conhecida como DMZ, zona desmilitarizada ou sub-rede filtrada) para ajudar a proteger aplicações com grupos de segurança de rede (NSGs).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Exemplo 2: Crie uma rede de perímetro para ajudar a proteger aplicações com uma firewall e NSGs.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Exemplo 3: Crie uma rede de perímetro para ajudar a proteger a redes com uma firewall, a rota definida pelo utilizador (UDR) e o NSG.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Exemplo 4: Adicione uma ligação híbrida com uma rede privada virtual (VPN) do dispositivo virtual, de site para site.](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exemplo 5: Adicione uma ligação híbrida com um gateway de VPN do Azure do site para site.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Exemplo 6: Adicione uma ligação híbrida com o ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Exemplos de adição de ligações entre redes virtuais, a elevada disponibilidade e o encadeamento de serviços serão adicionados a este documento através dos seguintes alguns meses.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Proteção de conformidade e a infraestrutura da Microsoft
Para ajudar as organizações a cumprir os requisitos da national, regionais e específicos da indústria que rege a recolha e utilização de dados dos utilizadores, a Microsoft oferece mais 40 certificações e attestations. O conjunto mais abrangente de qualquer fornecedor de serviço em nuvem.

Para obter mais informações, consulte as informações de compatibilidade no [Microsoft Trust Center][TrustCenter].

A Microsoft tem uma abordagem abrangente para proteger a infraestrutura de nuvem necessária para executar os serviços de hiper escala global. Infraestrutura de nuvem da Microsoft inclui hardware, software, redes e administrativos e os funcionários de operações, para além de centros de dados físico.

![2]

Esta abordagem fornece uma Fundação mais segura para os clientes implementar os seus serviços em nuvem da Microsoft. O passo seguinte é para os clientes conceber e criar uma arquitetura de segurança para proteger estes serviços.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Arquiteturas de segurança tradicionais e redes de perímetro
Embora a Microsoft invests descontos elevados a proteger a infraestrutura de nuvem, os clientes também terá de proteger os seus serviços em nuvem e os grupos de recursos. Uma abordagem multicamada para a segurança fornece a melhor defesa. Uma zona de segurança de rede de perímetro protege os recursos da rede interna de uma rede não fidedigna. Uma rede de perímetro refere-se para as margens ou partes da rede que manter-se entre a Internet e da empresa protegida infraestrutura de TI.

Nas redes empresarial típica, a infraestrutura de núcleo é fortemente fortified em perimeters, com várias camadas de dispositivos de segurança. O limite de cada camada é constituído por dispositivos e os pontos de imposição de política. Cada camada pode incluir uma combinação dos seguintes dispositivos de segurança de rede: firewalls, prevenção de Denial of Service (DoS), deteção de intrusões ou sistemas de proteção (IDS/IPS) e dispositivos VPN. A imposição de política pode ter a forma de políticas de firewall, listas de controlo de acesso (ACL) ou encaminhamento específico. A primeira linha de defesa na rede, aceitando diretamente o tráfego de entrada de Internet, é uma combinação destes mecanismos para ataques de blocos e o tráfego prejudicial permitindo ainda mais a pedidos legítimos para a rede. Este tráfego encaminha diretamente aos recursos na rede de perímetro. Esse recurso pode, em seguida, "conversar"com recursos mais profundo na rede, transiting o limite seguinte para a validação primeiro. A camada mais exterior é denominada rede de perímetro porque esta parte da rede está exposto à Internet, normalmente com alguma forma de proteção em ambos os lados. A figura seguinte mostra um exemplo de uma rede de perímetro de sub-rede única numa rede empresarial, com dois limites de segurança.

![3]

Existem muitas arquiteturas utilizadas para implementar uma rede de perímetro. Estes arquiteturas podem variar entre um balanceador de carga simples a uma rede de perímetro de múltiplas sub-redes com mecanismos variados em cada limite para o tráfego de bloco e proteger as camadas mais aprofundadas da rede empresarial. Como é criada a rede de perímetro depende as necessidades específicas da organização e a tolerância ao risco geral.

Como os clientes mover as cargas de trabalho para nuvens públicas, é fundamental para suportar funcionalidades semelhantes para a arquitetura de rede de perímetro no Azure para cumprir os requisitos de segurança e conformidade. Este documento fornece orientações sobre como os clientes podem criar um ambiente de rede segura no Azure. Concentra-se na rede de perímetro, mas também inclui um debate abrangente dos muitos aspetos de segurança de rede. As perguntas seguintes informam este debate:

* Como pode ser criada uma rede de perímetro no Azure?
* Quais são algumas das funcionalidades do Azure disponíveis para criar a rede de perímetro?
* Como podem proteger cargas de trabalho de back-end?
* Como as comunicações da Internet são controladas para cargas de trabalho do Azure?
* Como podem ser protegidas as redes no local de implementações no Azure?
* Quando devem ser utilizadas funcionalidades de segurança do Azure nativo versus serviços ou aplicações de terceiros?

O diagrama seguinte mostra várias camadas de segurança que Azure fornece aos clientes. Estas camadas são nativo na própria plataforma do Azure e funcionalidades do cliente:

![4]

Entrada da Internet, DDoS Azure ajuda a proteger contra ataques em grande escala contra do Azure. A próxima camada é definida pelo cliente endereços IP públicos (pontos finais), que são utilizados para determinar qual o tráfego pode passar o serviço em nuvem para a rede virtual. Nativo virtual do Azure isolamento de rede assegura isolamento completado de todas as outras redes e de que o tráfego apenas flui através dos métodos e caminhos de utilizador configurado. Estes caminhos e os métodos são a próxima camada, onde os NSGs, UDR e os dispositivos de rede virtual podem ser utilizados para criar limites de segurança para proteger as implementações de aplicações na rede protegida.

A secção seguinte fornece uma descrição geral das redes virtuais do Azure. Estas redes virtuais são criadas pelos clientes e são que as respetivas cargas de trabalho implementadas estão ligadas a. Redes virtuais são a base de todas as funcionalidades de segurança de rede necessárias para estabelecer a uma rede de perímetro a proteger as implementações de cliente no Azure.

## <a name="overview-of-azure-virtual-networks"></a>Descrição geral das redes virtuais do Azure
Antes de pode obter o tráfego de Internet para as redes virtuais do Azure, existem duas camadas de segurança inerentes à plataforma do Azure:

1.    **Proteção DDoS**: proteção DDoS é uma camada de rede física do Azure que protege a plataforma do Azure-se contra ataques baseados na Internet em grande escala. Estes ataques utilizam vários nós de "bot" numa tentativa para sobrecarregar um serviço de Internet. O Azure tem um mesh de proteção DDoS robusta da conectividade da região de entrada, saída e Azure entre todos os. Esta camada de proteção DDoS não tem nenhum atributos configurável de utilizador e não está acessível ao cliente. A camada de proteção DDoS protege do Azure como uma plataforma contra ataques em grande escala, que também monitoriza o tráfego out- vinculado e o tráfego de região do Azure em vários locais. Utilizar dispositivos de rede virtual na VNet, podem ser configuradas camadas adicionais de resiliência ao cliente contra um ataque de escala mais pequena que não trip a proteção ao nível da plataforma. Um exemplo de DDoS em ação; Se um internet com o endereço IP foi atacado por um ataque DDoS em grande escala, o Azure seria detetar as origens das ataques e limpar o tráfego inválido antes de que atingiu o destino pretendido. Praticamente todos os casos, o ponto final atacado não é afetado ataque. Nos casos raros que um ponto final é afetado, nenhum tráfego é afetado com outros pontos finais, apenas o ponto final atacado. Assim, outros clientes e serviços seriam Consulte sem afetar a partir desse ataque. É fundamental ter em atenção que o Azure DDoS apenas está à procura de ataques em grande escala. É possível que o serviço específico pode ser overwhelmed antes dos limiares de proteção ao nível da plataforma for excedidos. Por exemplo, um web site num único servidor A0 IIS, pode ser colocada offline por um ataque DDoS antes de nível de plataforma do Azure proteção DDoS registada uma ameaça.

2.  **Endereços IP públicos**: IP público endereços (ativados através de pontos finais de serviço, endereços IP públicos, Gateway de aplicação e outras funcionalidades do Azure que apresentam um endereço IP público para a internet encaminhado para o recurso) permitem que os serviços em nuvem ou recursos grupos de endereços IP de Internet pública e de portas expostas. O ponto final utiliza a tradução de endereços de rede (NAT) para encaminhar o tráfego para o endereço interno e a porta na rede virtual do Azure. Este caminho é a forma primária para o tráfego externo passar para a rede virtual. Os endereços IP públicos são configuráveis para determinar qual o tráfego é transmitido e como e onde é traduzido na rede virtual.

Assim que o tráfego chega a rede virtual, existem muitas funcionalidades que entrem em play. Redes virtuais do Azure são a base para os clientes ligar as respetivas cargas de trabalho e em que se aplica a básicos de segurança de nível de rede. É uma rede privada (uma sobreposição de rede virtual) no Azure para clientes com as seguintes funcionalidades e as características:

* **Isolamento de tráfego**: uma rede virtual é o limite de isolamento de tráfego na plataforma do Azure. Máquinas virtuais (VMs) numa rede virtual não pode comunicar diretamente para as VMs numa rede virtual diferente, mesmo se ambas as redes virtuais são criadas ao mesmo cliente. Isolamento é uma propriedade crítica que garante a VMs de cliente e comunicação permanece privada dentro de uma rede virtual.

>[!NOTE]
>Isolamento de tráfego refere-se apenas ao tráfego *entrada* à rede virtual. Por predefinição o tráfego de saída de VNet à internet é permitido, mas pode ser impedido se assim o desejar por NSGs.
>
>

* **Topologia multicamada**: redes virtuais permitem aos clientes definir topologia multicamada pela atribuição de sub-redes e designar espaços de endereços separadas para diferentes elementos ou "escalões" das respetivas cargas de trabalho. Estes agrupamentos lógicos topologias de permitem que os clientes definir a política de acesso diferentes com base nos tipos de carga de trabalho e também controlam fluxos de tráfego entre as camadas.
* **Conectividade entre instalações,**: os clientes podem estabelecer conectividade entre instalações, entre uma rede virtual e vários sites no local ou outras redes virtuais no Azure. Para construir uma ligação, os clientes podem utilizar o VNet Peering, Gateways de VPN do Azure, aplicações virtuais de rede de terceiros ou ExpressRoute. Azure suporta as VPNs (S2S) de site para site utilizar protocolos padrão de IPsec/IKE e conectividade privada do ExpressRoute.
* **NSG** permite aos clientes criar regras (ACLs) no nível pretendido de granularidade: as interfaces, VMs individuais ou sub-redes virtuais de rede. Os clientes podem controlar o acesso ao que permite ou nega comunicação entre as cargas de trabalho dentro de uma rede virtual, de sistemas na redes do cliente através de uma conectividade entre instalações, ou direcionar comunicação da Internet.
* **UDR** e **o reencaminhamento IP** permitem aos clientes definir os caminhos de comunicação entre camadas diferentes dentro de uma rede virtual. Os clientes podem implementar uma firewall, IDS/IPS e outras aplicações virtuais e encaminhar o tráfego de rede através destas aplicações de segurança para a imposição de política de limite de segurança, auditoria e inspeção.
* **Aplicações virtuais de rede** no Azure Marketplace: aplicações de segurança, tais como firewalls, balanceadores de carga e IDS/IPS estão disponíveis no Azure Marketplace e na Galeria de imagem de VM. Os clientes podem implementar estas aplicações para as respetivas redes virtuais e especificamente, nos respetivos limites de segurança (incluindo as sub-redes da rede de perímetro) para concluir um ambiente de várias camadas de rede seguro.

Com estas funcionalidades e capacidades, um exemplo de como é possível criar uma arquitetura de rede de perímetro no Azure é o diagrama a seguir:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Requisitos e as características de rede de perímetro
A rede de perímetro é o front-end da rede, diretamente interfacing comunicação da Internet. Os pacotes de entrada devem fluir através de aplicações de segurança, tais como a firewall, IDS e IPS, antes de chegar aos servidores back-end. Pacotes de vinculado à Internet das cargas de trabalho também possam circular através de aplicações de segurança na rede de perímetro para a imposição de política, inspeção e fins de auditorias, antes de deixarem a rede. Além disso, a rede de perímetro pode alojar gateways de VPN em vários locais entre redes virtuais do cliente e de redes no local.

### <a name="perimeter-network-characteristics"></a>Características de rede de perímetro
A referenciar a ilustração anterior, algumas das características da rede de perímetro boa são as seguintes:

* Para a Internet:
  * A sub-rede de rede de perímetro em si é através da Internet, a comunicar diretamente com a Internet.
  * Endereços IP públicos, VIPs e/ou pontos finais do serviço de passar o tráfego de Internet para a rede front-end e os dispositivos.
  * Tráfego de entrada da Internet passa através de dispositivos de segurança antes de outros recursos na rede front-end.
  * Se estiver ativada a segurança de saída, o tráfego passa através de dispositivos de segurança, como o passo final, antes de passar para a Internet.
* Rede protegida:
  * Não há nenhum caminho direto através da Internet para a infraestrutura de núcleo.
  * Canais para a infraestrutura de núcleo tem atravessar através de dispositivos de segurança, como os NSGs, firewalls ou dispositivos VPN.
  * Outros dispositivos não têm ponte Internet e a infraestrutura de núcleo.
  * Dispositivos de segurança o acesso à Internet e da rede protegida com acesso à limites de rede de perímetro (por exemplo, os dois firewall ícones mostrados na imagem anterior), na verdade, podem ser uma única aplicação virtual com regras diferenciadas ou interfaces para cada limite. Por exemplo, um dispositivo físico, logicamente separado, processar a carga de ambos os limites de rede de perímetro.
* Outras práticas e comuns restrições:
  * Cargas de trabalho não tem de armazenar informações críticas do negócio.
  * Acesso e atualizações às configurações de rede de perímetro e implementações estão limitadas a apenas os administradores autorizados.

### <a name="perimeter-network-requirements"></a>Requisitos de rede de perímetro
Para ativar estas características, siga estas diretrizes sobre os requisitos de rede virtual para implementar uma rede de perímetro com êxito:

* **Arquitetura de sub-rede:** especifique a rede virtual, de modo a que uma sub-rede toda dedicada que a rede de perímetro, separada das outras sub-redes na mesma rede virtual. Esta separação garante o tráfego entre a rede de perímetro e outros fluxos de camadas de sub-rede interno ou privado através de uma firewall ou os IDS/IPS de aplicação virtual.  As rotas definidas pelo utilizador nas sub-redes de limites são necessários para reencaminhar este tráfego para a aplicação virtual.
* **NSG:** a própria sub-rede de rede de perímetro deve ser aberta para permitir a comunicação com a Internet, mas que não significa que os clientes devem ser ignorando NSGs. Siga as práticas de segurança comuns para minimizar a analisa rede exposta à Internet. Bloqueie os intervalos de endereço remoto permitidos para aceder às implementações ou as portas que estão abertas e protocolos de aplicação específica. É possível que existam circunstâncias, no entanto, no qual um concluída bloqueio pendente não é possível. Por exemplo, se os clientes têm um Web site externo no Azure, a rede de perímetro deve permitir pedidos web recebidos de todos os endereços IP públicos, mas só deve abrir as portas de aplicação web: TCP na porta 80 e/ou o TCP na porta 443.
* **Tabela de encaminhamento:** a própria sub-rede de rede de perímetro deve ser capaz de comunicar diretamente à Internet, mas não deve permitir a comunicação direta de e para os back-end ou no local redes sem passar por uma firewall ou a segurança aplicação.
* **Configuração de aplicação de segurança:** para encaminhar e inspecionar os pacotes entre a rede de perímetro e o resto do protegida redes, os dispositivos de segurança, tais como a firewall, IDS, e os dispositivos IPS poderão multihomed. Podem ter NIC separadas para a rede de perímetro e as sub-redes de back-end. Os NICs na rede de perímetro comunicam diretamente de e para a Internet, com os NSGs correspondentes e a tabela de encaminhamento de rede de perímetro. Os NICs ligar às sub-redes de back-end tem mais restrito NSGs e tabelas de encaminhamento de sub-redes de back-end correspondentes.
* **Funcionalidade de segurança do dispositivo:** os dispositivos de segurança implementados na rede de perímetro, normalmente, efetuar as seguintes funcionalidades:
  * Firewall: Impor as regras de firewall ou políticas de controlo de acesso para pedidos recebidos.
  * Deteção e prevenção da ameaça: detetar e mitigar ataques maliciosos da Internet.
  * Auditoria e registo: manter registos detalhados para auditoria e análise.
  * Proxy inverso: Redireccionamento de pedidos recebidos para servidores de back-end correspondentes. Este redirecionamento envolve mapeamento e traduzir os endereços de destino nos dispositivos front-end, normalmente, firewalls, para os endereços de servidor back-end.
  * Reencaminhar proxy: fornecendo NAT e efetuar a auditoria para a comunicação iniciada a partir de dentro da rede virtual à Internet.
  * Router: Reencaminham o tráfego de entrada e entre sub-redes dentro da rede virtual.
  * Dispositivo VPN: a funcionar como gateways de VPN em vários locais para conectividade entre instalações, VPN entre as redes no local do cliente e redes virtuais do Azure.
  * Servidor VPN: aceitar os clientes VPN a ligar a redes virtuais do Azure.

> [!TIP]
> Manter os seguintes dois grupos separados: indivíduos autorizado a aceder o equipamento de segurança de rede de perímetro e os utilizadores autorizados como administradores de desenvolvimento, implementação ou as operações de aplicação. Manter estes grupos separados permite uma segregação de deveres e impede que uma única pessoa sem passar pela segurança de aplicações e controlos de segurança de rede.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Questões a pedido quando criar limites de rede
Nesta secção, salvo se especificamente mencionado, o termo "redes" refere-se a redes virtuais privadas do Azure criados por um administrador de subscrição. O termo não consulte as redes físicas subjacentes no Azure.

Além disso, as redes virtuais do Azure são frequentemente utilizados para expandir as redes tradicional no local. É possível incorporar o site para site ou soluções de rede híbrida ExpressRoute com arquiteturas de rede de perímetro. Esta ligação híbrida é uma consideração importante na criação de limites de segurança de rede.

As perguntas seguintes três são cruciais para responder quando estiver a criar uma rede com uma rede de perímetro e múltiplos limites de segurança.

#### <a name="1-how-many-boundaries-are-needed"></a>1) o número de limites são necessários?
É o primeiro ponto de decisão para decidir quantos limites de segurança são necessários num determinado cenário:

* Um único limite: uma rede de perímetro front-end, entre a rede virtual e a Internet.
* Limites de duas: um no lado Internet da rede de perímetro e outro entre a sub-rede de rede de perímetro e as sub-redes de back-end nas redes virtuais do Azure.
* Limites de três: sobre o lado de Internet da rede de perímetro, um entre a rede de perímetro e sub-redes de back-end e outra entre as sub-redes de back-end e a rede no local.
* Limites de N: um número de variável. Dependendo dos requisitos de segurança, não há nenhum limite para o número de limites de segurança que podem ser aplicadas numa determinada rede.

O número e tipo de limites necessários variam com base na tolerância ao risco da empresa e o cenário específico que está a ser implementado. Esta decisão, muitas vezes, é efetuada em conjunto com vários grupos de dentro de uma organização, incluindo, muitas vezes, uma equipa de risco e conformidade, uma rede e a equipa de plataforma e uma equipa de desenvolvimento de aplicações. As pessoas com conhecimentos de segurança, os dados envolvido e as tecnologias que está a ser utilizadas devem ter um indiquem esta decisão para garantir a postura perante o adequado de segurança para cada implementação.

> [!TIP]
> Utilize o número mais pequeno de limites que cumprem os requisitos de segurança para uma determinada situação. Limites mais, as operações e a resolução de problemas podem ser mais difícil, bem como os custos de gestão à gestão de políticas de limite de vários ao longo do tempo. No entanto, os limites insuficientes aumentam o risco. Encontrar o equilíbrio é fundamental.
>
>

![6]

A ilustração anterior mostra uma vista de alto nível de uma rede de limite de três segurança. Os limites são entre a rede de perímetro e a Internet, os Azure front-end e back-end sub-redes privadas e a sub-rede de back-end do Azure e da rede da empresa no local.

#### <a name="2-where-are-the-boundaries-located"></a>2) onde estão localizados os limites?
Depois do número de limites é decidir, onde pretende implementá-la é o próximo ponto de decisão. Normalmente, existem três opções:

* Utilizar um serviço intermediárias baseado na Internet (por exemplo, um baseado na nuvem firewall de aplicações Web, que não é abordada neste documento)
* Utilizar funcionalidades nativas e/ou os dispositivos de rede virtual no Azure
* Utilizar dispositivos físicos na rede no local

Em redes puramente do Azure, as opções são nativas funcionalidades do Azure (por exemplo, balanceadores de carga do Azure) ou virtual aparelhos de rede do ecossistema de parceiro avançado do Azure (por exemplo, as firewalls do ponto de verificação).

Se for necessário um limite entre o Azure e uma rede no local, os dispositivos de segurança podem residir em ambos os lados da ligação (ou ambos os lados). Assim uma decisão têm de ser efetuada na localização de colocar o equipamento de segurança.

Na figura anterior, a rede de Internet para o perímetro os limites de frente ao back-end são completamente contidos no Azure em tem de ser nativas funcionalidades do Azure ou rede aplicações virtuais. Dispositivos de segurança em limites entre o Azure (sub-rede de back-end) e a rede empresarial dever-se no lado do Azure ou o lado no local ou mesmo uma combinação de dispositivos em ambos os lados. Pode ser significativas vantagens e desvantagens para qualquer uma das opções que tem de ser seriamente considerado.

Por exemplo, utilizar o equipamento de segurança física existente no lado de rede no local tem a vantagem que não é necessária nenhuma equipamento de novo. Apenas necessita de reconfiguração. A desvantagem, no entanto, é que todo o tráfego deve voltar a partir do Azure para a rede no local para ser visualizadas pelo equipamento de segurança. Assim o tráfego do Azure para o Azure pode implicar latência significativa e o efeito do desempenho da aplicação e experiência do utilizador, se esta foi forçada à rede no local para a imposição de política de segurança.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) como são implementados limites?
Cada limite de segurança, provavelmente, terá dos requisitos de capacidade diferente (por exemplo, IDS e regras de firewall no lado de Internet da rede de perímetro, mas apenas ACLs entre a rede de perímetro e a sub-rede de back-end). Decidir onde dispositivo (ou o número de dispositivos) para utilizar depende dos requisitos de cenário e segurança. Na secção seguinte, exemplos 1, 2 e 3 abordam algumas opções que pode ser utilizadas. Rever as funcionalidades de rede nativo do Azure e os dispositivos disponíveis no Azure a partir do ecossistema de parceiro mostra as opções de conjunto disponíveis para resolver praticamente qualquer cenário.

Outro ponto de decisão de implementação chave é como ligar a rede no local com o Azure. Deve utilizar o gateway virtual do Azure ou uma aplicação virtual de rede? Estas opções são abordadas mais detalhadamente na secção seguinte (exemplos 4, 5 e 6).

Além disso, o tráfego entre redes virtuais no Azure pode ser necessárias. Estes cenários serão adicionados no futuro.

Quando souber as respostas às perguntas anterior, o [rápido iniciar](#fast-start) secção pode ajudar a identificar os exemplos são mais adequados para um determinado cenário.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exemplos: Criar limites de segurança com redes virtuais do Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exemplo 1 criar uma rede de perímetro para ajudar a proteger aplicações com NSGs
[Voltar ao início rápido](#fast-start) | [detalhados criar instruções para este exemplo][Example1]

[![7]][7]

#### <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, se existe uma subscrição que contém os seguintes recursos:

- Um grupo de recursos única
- Uma rede virtual com duas sub-redes: "FrontEnd" e "BackEnd"
- Um grupo de segurança de rede que é aplicada a ambas as sub-redes
- Um servidor do Windows que representa um servidor de web de aplicação ("IIS01")
- Dois servidores do Windows que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
- Um servidor do Windows que representa um servidor DNS ("DNS01")
- Um IP público associado ao servidor de web de aplicação

Para scripts e um modelo Azure Resource Manager, consulte o [compilação instruções detalhadas][Example1].

#### <a name="nsg-description"></a>Descrição do NSG
Neste exemplo, um grupo NSG está incorporado e, em seguida, carregar com regras de seis.

> [!TIP]
> Um modo geral, deve criar as regras de "Permitir" específicas em primeiro lugar, seguido de regras de "Deny" mais genéricas. A prioridade atribuída determinam as regras são avaliadas primeiro. Assim que o tráfego for encontrado para aplicar a uma regra específica, não existem regras adicionais são avaliadas. Podem aplicar regras do NSG em qualquer direção entrada ou de saída (da perspetiva da sub-rede).
>
>

De forma declarativa, as seguintes regras estão a ser criadas para o tráfego de entrada:

1. O tráfego DNS interno (porta 53) é permitido.
2. Tráfego RDP de (porta 3389) da Internet para qualquer máquina Virtual é permitido.
3. Tráfego HTTP (porta 80) da Internet para o servidor web (IIS01) é permitido.
4. Qualquer tráfego (todas as portas) do IIS01 para AppVM1 é permitido.
5. Qualquer tráfego (todas as portas) da Internet para a rede virtual completo (ambas as sub-redes) é negado.
6. Qualquer tráfego (todas as portas) partir da sub-rede do front-end para a sub-rede de back-end é negado.

Com estas regras vinculada a cada sub-rede, se um pedido de HTTP foi recebido a partir da Internet para o servidor web, ambas as regras de 3 (permitir) e 5 (negar) seria aplicada. Mas porque a regra 3 tem uma prioridade mais alta, apenas seria aplicada e regra 5 não seria entrem em play. Deste modo, os pedidos de HTTP seriam permissão para ser o servidor web. Se esse mesmo tráfego estava a tentar aceder ao servidor DNS01, regra 5 (negar) seria o primeiro a aplicar e o tráfego seria não autorizado para o servidor. Regra 6 (negar) bloqueia a sub-rede do front-end de falar com a sub-rede de back-end (exceto para o tráfego permitido nas regras de 1 e 4). Este conjunto de regras protege a rede de back-end no caso de um atacante compromises a aplicação web front-end. O atacante poderia limitada acesso à rede "protegido" back-end (apenas para recursos expostos no servidor de AppVM01).

Há uma regra de saída predefinida que permita o tráfego de saída à Internet. Neste exemplo, vamos está a permitir o tráfego de saída e não modificar quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, encaminhamento definido pelo utilizador é necessário (consulte exemplo 3).

#### <a name="conclusion"></a>Conclusão
Neste exemplo é uma forma relativamente simple e fácil de isolar a sub-rede de back-end do tráfego de entrada. Para obter mais informações, consulte o [compilação instruções detalhadas][Example1]. Estas instruções incluem:

* Como criar esta rede de perímetro com scripts do PowerShell clássicos.
* Como criar esta rede de perímetro com um modelo Azure Resource Manager.
* Descrições detalhadas de cada comando NSG.
* Cenários de fluxo do tráfego de detalhado, que mostra como o tráfego é permitido ou negado em cada camada.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2 criar uma rede de perímetro para ajudar a proteger aplicações com uma firewall e NSGs
[Voltar ao início rápido](#fast-start) | [detalhados criar instruções para este exemplo][Example2]

[![8]][8]

#### <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, se existe uma subscrição que contém os seguintes recursos:

* Um grupo de recursos única
* Uma rede virtual com duas sub-redes: "FrontEnd" e "BackEnd"
* Um grupo de segurança de rede que é aplicada a ambas as sub-redes
* Uma aplicação virtual de rede, neste caso, a firewall, ligado à sub-rede do front-end
* Um servidor do Windows que representa um servidor de web de aplicação ("IIS01")
* Dois servidores do Windows que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")

Para scripts e um modelo Azure Resource Manager, consulte o [compilação instruções detalhadas][Example2].

#### <a name="nsg-description"></a>Descrição do NSG
Neste exemplo, um grupo NSG está incorporado e, em seguida, carregar com regras de seis.

> [!TIP]
> Um modo geral, deve criar as regras de "Permitir" específicas em primeiro lugar, seguido de regras de "Deny" mais genéricas. A prioridade atribuída determinam as regras são avaliadas primeiro. Assim que o tráfego for encontrado para aplicar a uma regra específica, não existem regras adicionais são avaliadas. Podem aplicar regras do NSG em qualquer direção entrada ou de saída (da perspetiva da sub-rede).
>
>

De forma declarativa, as seguintes regras estão a ser criadas para o tráfego de entrada:

1. O tráfego DNS interno (porta 53) é permitido.
2. Tráfego RDP de (porta 3389) da Internet para qualquer máquina Virtual é permitido.
3. Qualquer tráfego de Internet (todas as portas) para a aplicação virtual de rede (firewall) é permitido.
4. Qualquer tráfego (todas as portas) do IIS01 para AppVM1 é permitido.
5. Qualquer tráfego (todas as portas) da Internet para a rede virtual completo (ambas as sub-redes) é negado.
6. Qualquer tráfego (todas as portas) partir da sub-rede do front-end para a sub-rede de back-end é negado.

Com estas regras vinculada a cada sub-rede, se um pedido de HTTP foi entrado a partir da Internet para a firewall, ambas as regras de 3 (permitir) e 5 (negar) seria aplicada. Mas porque a regra 3 tem uma prioridade mais alta, apenas seria aplicada e regra 5 não seria entrem em play. Deste modo, os pedidos de HTTP seriam permissão para ser a firewall. Se esse mesmo tráfego estava a tentar aceder ao servidor IIS01, apesar de estar na sub-rede front-end, regra 5 (negar) seria aplicada, e o tráfego seria não autorizado para o servidor. Regra 6 (negar) bloqueia a sub-rede do front-end de falar com a sub-rede de back-end (exceto para o tráfego permitido nas regras de 1 e 4). Este conjunto de regras protege a rede de back-end no caso de um atacante compromises a aplicação web front-end. O atacante poderia limitada acesso à rede "protegido" back-end (apenas para recursos expostos no servidor de AppVM01).

Há uma regra de saída predefinida que permita o tráfego de saída à Internet. Neste exemplo, vamos está a permitir o tráfego de saída e não modificar quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, encaminhamento definido pelo utilizador é necessário (consulte exemplo 3).

#### <a name="firewall-rule-description"></a>Descrição da regra de firewall
Na firewall, as regras de reencaminhamento deverá ser criado. Uma vez que este exemplo apenas encaminha o tráfego de Internet no vinculados à firewall e, em seguida, para o servidor web, de reencaminhamento apenas uma rede (NAT) de tradução de endereços é necessária a regra.

A regra de reencaminhamento aceita qualquer endereço de origem de entrada que chega a firewall a tentar aceder a HTTP (porta 80 ou 443 para HTTPS). Tem enviados sem interface local da firewall e redirecionado para o servidor web com o endereço IP do 10.0.1.5.

#### <a name="conclusion"></a>Conclusão
Neste exemplo é uma forma de proteger a sua aplicação com uma firewall e isolamento da sub-rede de back-end do tráfego de entrada relativamente simples. Para obter mais informações, consulte o [compilação instruções detalhadas][Example2]. Estas instruções incluem:

* Como criar esta rede de perímetro com scripts do PowerShell clássicos.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Descrições detalhadas de cada regra de comando e de firewall NSG.
* Cenários de fluxo do tráfego de detalhado, que mostra como o tráfego é permitido ou negado em cada camada.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Exemplo 3 criar uma rede de perímetro para ajudar a proteger a redes com uma firewall e UDR e NSG
[Voltar ao início rápido](#fast-start) | [detalhados criar instruções para este exemplo][Example3]

[![9]][9]

#### <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, se existe uma subscrição que contém os seguintes recursos:

* Um grupo de recursos única
* Uma rede virtual com três sub-redes: "SecNet", "FrontEnd" e "BackEnd"
* Uma aplicação virtual de rede, neste caso, a firewall, ligado à sub-rede SecNet
* Um servidor do Windows que representa um servidor de web de aplicação ("IIS01")
* Dois servidores do Windows que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")

Para scripts e um modelo Azure Resource Manager, consulte o [compilação instruções detalhadas][Example3].

#### <a name="udr-description"></a>Descrição de UDR
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

O VNETLocal é sempre um ou mais definidos os prefixos de endereços que compõem a rede virtual para essa rede específica (ou seja, as alterações a partir da rede virtual a rede virtual, dependendo de como cada rede virtual específico está definido). As rotas de sistema restantes são estáticas e predefinida como indicado na tabela.

Neste exemplo, duas tabelas de encaminhamento são criados, um cada para as sub-redes de front-end e back-end. Cada tabela é carregada com rotas estáticas adequadas para a sub-rede especificada. Neste exemplo, cada tabela tenha três rotas que direcionam todo o tráfego (0.0.0.0/0) através da firewall (próximo salto = o endereço IP do dispositivo Virtual):

1. Tráfego de sub-rede local com nenhuma próximo salto definido para permitir tráfego de sub-rede local para ignorar a firewall.
2. Tráfego de rede virtual com um salto seguinte definido como firewall. Este salto seguinte substitui a regra predefinida que permita o tráfego de rede virtual local encaminhar diretamente.
3. Todos os restantes tráfego (0/0) com um salto seguinte definida como a firewall.

> [!TIP]
> Não ter a entrada de sub-rede local as comunicações do UDR quebras sub-rede local.
>
> * No nosso exemplo, é fundamental 10.0.1.0/24 apontar para VNETLocal! Sem ela, pacote abandonar o fileparser o servidor Web (10.0.1.4) destinado ao outro servidor local (por exemplo) 10.0.1.25 irá falhar, uma vez que serão enviadas para a NVA. A NVA enviará-lo para a sub-rede e a sub-rede será reenviem-Mo para a NVA num ciclo infinito.
> * As possibilidades de um ciclo de encaminhamento são normalmente superiores em aplicações com vários NICs estão ligadas a separar as sub-redes, que é frequentemente de aparelhos do tradicional, no local.
>
>

Depois de criar as tabelas de encaminhamento, tem de ser vinculadas para as suas sub-redes. A tabela de encaminhamento para a sub-rede do front-end como, uma vez criada e vinculado à sub-rede, seria aspeto este resultado:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> UDR agora pode ser aplicada para a sub-rede do gateway no qual está ligado o circuito ExpressRoute.
>
> Exemplos de como ativar a sua rede de perímetro com o ExpressRoute ou redes de site a site são apresentados nos exemplos 3 e 4.
>
>

#### <a name="ip-forwarding-description"></a>Descrição de reencaminhamento IP
O reencaminhamento IP é uma funcionalidade de complemento para UDR. O reencaminhamento IP é uma definição de uma aplicação virtual que permite receba tráfego endereçado não especificamente para a aplicação e, em seguida, reencaminhar o tráfego para o destino ultimate.

Por exemplo, se AppVM01 faz um pedido para o servidor de DNS01, UDR seria encaminhar este tráfego para a firewall. Com o reencaminhamento IP ativada, o tráfego para o destino de DNS01 (10.0.2.4) é aceite pelo dispositivo de (10.0.0.4) e, em seguida, é reencaminhado para o destino ultimate (10.0.2.4). Sem o reencaminhamento IP ativada na firewall, o tráfego seria não aceite pelo dispositivo de apesar da tabela de rota tem de firewall como salto seguinte. Para utilizar uma aplicação virtual, é extremamente importante lembrar-se de ativar o reencaminhamento IP juntamente com UDR.

#### <a name="nsg-description"></a>Descrição do NSG
Neste exemplo, um grupo NSG está incorporado e, em seguida, carregado com uma única regra. Este grupo, em seguida, está vinculado apenas para as sub-redes de front-end e back-end (não o SecNet). Modo declarativo está a ser criada a seguinte regra:

* Qualquer tráfego (todas as portas) da Internet para a rede virtual completo (todas as sub-redes) é negado.

Embora os NSGs são utilizados neste exemplo, o objetivo principal é como uma camada secundária de defesa contra manual configuração incorreta. O objetivo é para bloquear todo o tráfego de entrada da Internet para as sub-redes front-end ou back-end. Apenas o tráfego deve fluir pelo sub-rede SecNet da firewall (e, em seguida, se adequado, para as sub-redes de front-end ou back-end). Plus, com as regras UDR no local, qualquer tráfego que torná-lo para as sub-redes de front-end ou back-end seria direcionado enviados para a firewall (graças ao UDR). A firewall poderá ver este tráfego como um fluxo assimétrico e teria de remover o tráfego de saída. Deste modo, existem três camadas de segurança proteger as sub-redes:

* Não existem endereços IP públicos em qualquer front-end ou back-end NICs.
* NSGs negar o tráfego da Internet.
* O firewall remover assimétrico tráfego.

Um ponto de interessante sobre o NSG neste exemplo é que contém apenas uma regra, o que está a negar o tráfego de Internet para a rede virtual completo, incluindo a sub-rede de segurança. No entanto, uma vez que o NSG só está vinculado às sub-redes de front-end e back-end, a regra não é processada sobre o tráfego de entrada para a sub-rede de segurança. Como resultado, o tráfego flui para a sub-rede de segurança.

#### <a name="firewall-rules"></a>Regras da firewall
Na firewall, as regras de reencaminhamento deverá ser criado. Uma vez que a firewall a bloquear ou o reencaminhamento toda a entrada, saído e o tráfego de rede intra-virtual, são necessárias várias regras de firewall. Além disso, todo o tráfego de entrada pedidos com êxito o endereço IP público do serviço de segurança (em portas diferentes), para ser processado pela firewall. Uma melhor prática é para os fluxos lógicos antes de configurar as sub-redes de diagrama e regras de firewall, para evitar rework mais tarde. A figura seguinte é uma vista lógica das regras de firewall para este exemplo:

![10]

> [!NOTE]
> As portas de gestão com base no dispositivo Virtual de rede utilizada, variam. Neste exemplo, é referenciada um Barracuda NextGen Firewall, que utiliza as portas 22, 801 e 807. Consulte a documentação do fornecedor de aplicação para localizar as portas exatas utilizadas para a gestão do dispositivo a ser utilizado.
>
>

#### <a name="firewall-rules-description"></a>Descrição de regras de firewall
A sub-rede de segurança não é apresentada no diagrama lógico anterior, porque a firewall está o recurso apenas dessa sub-rede. O diagrama é que mostra as regras de firewall e como logicamente permitir ou negar fluxos de tráfego, não o caminho encaminhado real. Além disso, as portas externas, selecionadas para o tráfego RDP são superior ranged portas (8014 – 8026) e não foram selecionadas para aproximadamente ligado alinhar com os dois últimos octetos do endereço IP local para o facilitar a leitura mais fácil (por exemplo, endereço de servidor local 10.0.1.4 está associado porta externa 8014). Qualquer superiores portas não esteja em conflito, no entanto, é possível utilizar.

Neste exemplo, precisamos de sete tipos de regras:

* Regras de externas (para o tráfego de entrada):
  1. Regra de firewall de gestão: redirecionamento de aplicação esta regra permite que o tráfego passar para as portas de gestão do dispositivo virtual de rede.
  2. Regras RDP (para cada servidor do Windows): estas quatro regras (um para cada servidor) permitem a gestão dos servidores individuais através do RDP. Também podem ser fechadas quatro regras RDP para uma regra, consoante as capacidades do dispositivo virtual de rede que está a ser utilizado.
  3. As regras de tráfego de aplicação: Existem duas estas regras, o primeiro para o tráfego web front-end e o segundo para o tráfego de back-end (por exemplo, servidor web para a camada de dados). A configuração destas regras depende a arquitetura de rede (em que os servidores são colocados) e fluxos de tráfego (a direção fluxos de tráfego e que portas são utilizadas).
     * A primeira regra permite o tráfego de aplicação real alcançar o servidor de aplicação. Enquanto outras regras permitem a gestão e segurança, as regras de tráfego de aplicação são que permitir que os utilizadores externos ou os serviços para aceder às aplicações. Neste exemplo, não há um servidor web único na porta 80. Deste modo, uma única aplicação regra de firewall redireciona o tráfego de entrada para o IP externo, para o endereço IP da interno de servidores web. A sessão de tráfego redirecionada seria converter através de NAT do servidor interno.
     * A segunda regra é a regra de back-end para permitir que o servidor web para que comunique com o servidor de AppVM01 (mas não AppVM02) através de qualquer porta.
* Regras internas (para o tráfego de rede intra-virtual)
  1. Saída para a regra de Internet: esta regra permite o tráfego de qualquer rede para passar para as redes selecionadas. Esta regra é, normalmente, uma regra predefinida já na firewall, mas num estado desativado. Esta regra deve ser ativada para este exemplo.
  2. Regra de DNS: esta regra permite apenas tráfego DNS (porta 53) passar para o servidor DNS. Para este ambiente, a maioria do tráfego de front-end para o back-end está bloqueada. Esta regra permite especificamente DNS a partir de qualquer sub-rede local.
  3. Sub-rede a regra de sub-rede: esta regra é permitir que qualquer servidor na sub-rede de back-end para ligar a nenhum servidor na sub-rede do front-end (mas não o inverso).
* Obter regra isento de falhas (para o tráfego que não cumpre nenhum dos anterior):
  1. Negar a regra de tráfego de todos os: esta regra de negação deve ser sempre a regra final (em termos de prioridade) e, como tal, se um fluxo de tráfego não corresponde a nenhum dos precedente regras é removido por esta regra. Esta regra é uma regra predefinida e, normalmente, no local e Active Directory. Sem modificações normalmente, são necessárias para esta regra.

> [!TIP]
> Na segunda regra de tráfego de aplicação, para simplificar neste exemplo, qualquer porta é permitido. Num cenário real, a porta mais específica e intervalos de endereços devem ser utilizados para reduzir a superfície de ataque desta regra.
>
>

Assim que são criadas as regras de anteriores, é importante rever a prioridade de cada regra para garantir que o tráfego é permitido ou negado conforme pretendido. Neste exemplo, as regras são ordem de prioridade.

#### <a name="conclusion"></a>Conclusão
Neste exemplo é uma mas mais complexas concluir a forma de proteger e isolamento da rede que exemplos anteriores. (Exemplo 2 protege apenas a aplicação e exemplo 1 apenas isola as sub-redes). Esta estrutura permite a monitorização de tráfego em ambas as direções e protege não apenas o servidor de aplicação de entrada, mas impõe a política de segurança de rede para todos os servidores nesta rede. Além disso, consoante o dispositivo utilizado, o tráfego total de auditoria e deteção podem ser alcançados. Para obter mais informações, consulte o [compilação instruções detalhadas][Example3]. Estas instruções incluem:

* Como criar esta rede de perímetro de exemplo com scripts do PowerShell clássicos.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Descrições de cada UDR, NSG de detalhado comando e a regra de firewall.
* Cenários de fluxo do tráfego de detalhado, que mostra como o tráfego é permitido ou negado em cada camada.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Exemplo 4 adicionar uma ligação híbrida com um dispositivo VPN site a site e virtual
[Voltar ao início rápido](#fast-start) | Detalhadas instruções de compilação disponível em breve

[![11]][11]

#### <a name="environment-description"></a>Descrição de ambiente
Rede híbrida através de uma aplicação virtual de rede (NVA) pode ser adicionada a nenhum dos tipos de rede de perímetro descritos nos exemplos 1, 2 ou 3.

Como é mostrado na figura anterior, uma ligação VPN através da Internet (site a site) é utilizada para ligar a uma rede no local a uma rede virtual do Azure através de uma NVA.

> [!NOTE]
> Se utilizar o ExpressRoute com a opção de Peering público do Azure ativada, uma rota estática deverá ser criada. Esta rota estática deve encaminhar para o endereço IP de VPN NVA fora da sua empresa Internet e não através da ligação do ExpressRoute. O NAT necessário na opção de Peering público do ExpressRoute do Azure pode interromper a sessão VPN.
>
>

Assim que estiver a VPN no local, a NVA torna-se o concentrador central para todas as redes e sub-redes. As regras de reencaminhamento de firewall determinam os fluxos de tráfego são permitidos, são convertidas através de NAT, são redirecionadas ou são ignoradas (mesmo para fluxos de tráfego entre a rede no local e o Azure).

Fluxos de tráfego devem ser considerados cuidadosamente, à medida que podem ser otimizados ou degradado por este padrão de conceção, consoante o específicos caso de utilização.

Utilizando o ambiente incorporado no exemplo 3 e, em seguida, adicionar uma ligação de rede do site para site VPN híbrida, produz a estrutura seguinte:

[![12]][12]

O router no local, ou qualquer outro dispositivo de rede que é compatível com a NVA para VPN, seria o cliente VPN. Este dispositivo físico seria responsável por iniciar e manter a ligação VPN com o seu NVA.

Logicamente para a NVA na rede aspeto quatro separado "zonas de segurança" com as regras no NVA a ser o diretório principal do tráfego entre estes zonas:

![13]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede híbrida do site para site VPN a uma rede virtual do Azure pode expandir a rede no local no Azure de forma segura. Utilizar uma ligação VPN, o tráfego é encriptado e encaminha através da Internet. A NVA neste exemplo fornece uma localização central para impor a e gerir a política de segurança. Para obter mais informações, consulte as instruções detalhadas de compilação (lançamento). Estas instruções incluem:

* Como criar esta rede de perímetro de exemplo com scripts do PowerShell.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Cenários de fluxo do tráfego de detalhado, que mostra como o tráfego flui através desta estrutura.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Exemplo 5 adicionar uma ligação híbrida com um gateway de VPN do Azure site a site
[Voltar ao início rápido](#fast-start) | Detalhadas instruções de compilação disponível em breve

[![14]][14]

#### <a name="environment-description"></a>Descrição de ambiente
Rede híbrida utilizando um gateway de VPN do Azure pode ser adicionada a cada tipo de rede de perímetro descritos nos exemplos 1 ou 2.

Como é mostrado na figura anterior, uma ligação VPN através da Internet (site a site) é utilizada para ligar a uma rede no local a uma rede virtual do Azure através de um gateway de VPN do Azure.

> [!NOTE]
> Se utilizar o ExpressRoute com a opção de Peering público do Azure ativada, uma rota estática deverá ser criada. Esta rota estática deve encaminhar para o endereço IP de VPN NVA fora da sua empresa Internet e não através de WAN o ExpressRoute. O NAT necessário na opção de Peering público do ExpressRoute do Azure pode interromper a sessão VPN.
>
>

A figura seguinte mostra as duas extremidades em rede neste exemplo. No primeiro limite, a NVA NSGs controlam e fluxos de tráfego para redes intra-do Azure e entre o Azure e a Internet. O contorno segundo é o gateway de VPN do Azure, que tem um limite de rede separada e isoladas entre no local e o Azure.

Fluxos de tráfego devem ser considerados cuidadosamente, à medida que podem ser otimizados ou degradado por este padrão de conceção, consoante o específicos caso de utilização.

Utilizando o ambiente incorporado no exemplo 1 e, em seguida, adicionar uma ligação de rede do site para site VPN híbrida, produz a estrutura seguinte:

[![15]][15]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede híbrida do site para site VPN a uma rede virtual do Azure pode expandir a rede no local no Azure de forma segura. Utilizar o gateway de VPN do Azure nativo, o tráfego é encriptado IPSec e encaminha através da Internet. Além disso, se utilizar o gateway de VPN do Azure, pode fornecer uma opção de custo inferior (nenhum licenciamento adicional custo tal como acontece com terceiros NVAs). Esta opção é mais económica exemplo 1, onde nenhum NVA é utilizado. Para obter mais informações, consulte as instruções detalhadas de compilação (lançamento). Estas instruções incluem:

* Como criar esta rede de perímetro de exemplo com scripts do PowerShell.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Cenários de fluxo do tráfego de detalhado, que mostra como o tráfego flui através desta estrutura.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exemplo 6 adicionar uma ligação híbrida com o ExpressRoute
[Voltar ao início rápido](#fast-start) | Detalhadas instruções de compilação disponível em breve

[![16]][16]

#### <a name="environment-description"></a>Descrição de ambiente
Rede híbrida através de uma ligação de peering privada do ExpressRoute pode ser adicionada a cada tipo de rede de perímetro descritos nos exemplos 1 ou 2.

Como é mostrado na figura anterior, o peering privado do ExpressRoute fornece uma ligação direta entre a rede no local e a rede virtual do Azure. Tráfego transits apenas a rede de fornecedor de serviço e a rede do Microsoft Azure, nunca tocar da Internet.

> [!TIP]
> Com o ExpressRoute mantém o tráfego de rede empresarial desligado da Internet. Também permite para contratos de nível de serviço através do seu fornecedor de ExpressRoute. O Gateway do Azure pode passar até 10 Gbps com o ExpressRoute, enquanto com VPNs de site a site, o débito máximo de Gateway do Azure é de 200 Mbps.
>
>

Como mostrado no diagrama seguinte, com esta opção o ambiente tem agora duas extremidades em rede. A NVA NSG controlo e fluxos de tráfego para redes intra-do Azure e entre o Azure e a Internet, enquanto o gateway é um limite de rede separada e isoladas entre no local e o Azure.

Fluxos de tráfego devem ser considerados cuidadosamente, à medida que podem ser otimizados ou degradado por este padrão de conceção, consoante o específicos caso de utilização.

Utilizando o ambiente incorporado no exemplo 1 e, em seguida, adicionar uma ligação de rede híbrida do ExpressRoute, produz a estrutura seguinte:

[![17]][17]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede do Peering de ExpressRoute privada pode expandir a rede no local no Azure latência segura, inferior, superior a forma de efetuar. Além disso, utilizar o Gateway do Azure nativo, tal como neste exemplo, fornece uma opção de custo inferior (às licenciamento tal como acontece com terceiros NVAs não adicionais). Para obter mais informações, consulte as instruções detalhadas de compilação (lançamento). Estas instruções incluem:

* Como criar esta rede de perímetro de exemplo com scripts do PowerShell.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Cenários de fluxo do tráfego de detalhado, que mostra como o tráfego flui através desta estrutura.

## <a name="references"></a>Referências
### <a name="helpful-websites-and-documentation"></a>Web sites úteis e a documentação
* Azure de acesso com o Azure Resource Manager:
* Aceder ao Azure com o PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Documentação das redes virtuais: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Documentação do grupo de segurança de rede: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/virtual-networks-nsg.md)
* Documentação de encaminhamento definido pelo utilizador: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Gateways da virtual do Azure: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Site-Site VPNs: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Documentação do ExpressRoute (não se esqueça de verificar as secções "Introdução" e "Como para"): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "fluxograma de opções de segurança"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "funcionalidades de segurança do azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "A rede de Perímetro numa rede empresarial"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "arquitetura de segurança do azure"
[5]: ./media/best-practices-network-security/dmzazure.png "DMZ numa rede Virtual do Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "rede híbrida com três limites de segurança"
[7]: ./media/best-practices-network-security/example1design.png "entrada DMZ com o NSG"
[8]: ./media/best-practices-network-security/example2design.png "DMZ com NVA e NSG de entrada"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "vista lógica das regras de Firewall"
[11]: ./media/best-practices-network-security/example3designoptions.png "DMZ com NVA ligado rede híbrida"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ com NVA ligado através de uma VPN de Site a Site"
[13]: ./media/best-practices-network-security/example4networklogical.png "rede lógica da perspetiva NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ com o Gateway do Azure ligado rede híbrida de Site a Site"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ com o Gateway do Azure através da VPN de Site a Site"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ com o Gateway do Azure ligado rede híbrida de ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ com o Gateway do Azure através de uma ligação de ExpressRoute"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
