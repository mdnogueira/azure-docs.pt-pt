<properties 
   pageTitle="O que é um Grupo de Segurança de Rede (NSG)"
   description="Saiba mais sobre a firewall distribuída no Azure através de Grupos de Segurança de Rede (NSGs) e como utilizar os NSGs para isolar e controlar o fluxo de tráfego nas redes virtuais (VNets)."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />


# O que é um Grupo de Segurança de Rede (NSG)?

O Grupo de Segurança de Rede (NSG) contém uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede para as instâncias de VM numa Rede Virtual. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede. Além disso, o tráfego para uma VM individual pode ser mais restringido ao associar um NSG diretamente a essa VM.

## Recurso NSG

Os NSGs contêm as seguintes propriedades.

|Propriedade|Descrição|Restrições|Considerações|
|---|---|---|---|
|Nome|Nome para o NSG|Tem de ser exclusivo dentro da região<br/>Pode conter letras, números, carateres de sublinhado, pontos finais e hífenes<br/>Tem de começar com uma letra ou um número<br/>Tem de terminar com uma letra, um número ou um caráter de sublinhado<br/>Pode ter até 80 carateres|Uma vez que poderá ser necessário criar vários NSGs, certifique-se de que tem uma convenção de nomenclatura que facilite a identificação da função dos NSGs|
|Região|A região do Azure onde está alojado o NSG|Os NSGs só podem ser aplicados a recursos na região que é criada|Consulte os [limites](#Limits) abaixo para compreender quantos NSGs pode ter numa região|
|Grupo de recursos|O grupo de recursos ao qual o NSG pertence|Embora um NSG pertença a um grupo de recursos, pode ser associado a recursos em qualquer grupo de recursos, desde que o recurso faça parte da mesma região do Azure que o NSG|Os grupos de recursos são utilizados para gerir vários recursos em conjunto, como uma unidade de implementação<br/>Poderá considerar agrupar o NSG aos recursos a que está associado|
|Regras|Regras que definem qual o tráfego permitido ou negado||Consulte as [regras do NSG](#Nsg-rules) abaixo| 

>[AZURE.NOTE] As ACLs baseadas em ponto final e os grupos de segurança de rede não são suportados na mesma instância de VM. Se pretender utilizar um NSG e ter uma ACL de ponto final já implementada, remova primeiro a ACL de ponto final. Para obter informações sobre como fazê-lo, consulte o artigo [Gerir Listas de Controlo de Acesso (ACL) para Pontos Finais utilizando o PowerShell](virtual-networks-acl-powershell.md).

### Regras do NSG

As regras do NSG contêm as seguintes propriedades.

|Propriedade|Descrição|Restrições|Considerações|
|---|---|---|---|
|**Nome**|Nome para a regra|Tem de ser exclusivo dentro da região<br/>Pode conter letras, números, carateres de sublinhado, pontos finais e hífenes<br/>Tem de começar com uma letra ou um número<br/>Tem de terminar com uma letra, um número ou um caráter de sublinhado<br/>Pode ter até 80 carateres|Pode ter várias regras dentro de um NSG, por isso, certifique-se de que segue uma convenção de nomenclatura que lhe permita identificar a função da sua regra|
|**Protocolo**|Protocolo a corresponder para a regra|TCP, UDP ou \*|Utilizar \* como um protocolo inclui o ICMP (apenas tráfego Este-Oeste), assim como o UDP e TCP e pode reduzir o número de regras necessárias<br/>Ao mesmo tempo, utilizar \* poderá ser uma abordagem demasiado abrangente, por isso, certifique-se de que utiliza apenas quando for realmente necessário|
|**Intervalo de portas de origem**|O intervalo de portas de origem a corresponder para a regra|Número de porta individual entre 1 e 65535, intervalo de portas (ou seja, 1-65635) ou \* (para todas as portas)|As portas de origem podem ser efémeras. A menos que o seu programa cliente esteja a utilizar uma porta específica, utilize "*" na maioria dos casos.<br/>Tente utilizar intervalos de portas sempre que possível para evitar a necessidade de várias regras<br/>Várias portas ou intervalos de portas não podem ser agrupados por uma vírgula
|**Intervalo de portas de destino**|O intervalo de portas de destino a corresponder para a regra|Número de porta individual entre 1 e 65535, intervalo de portas (ou seja, 1-65535) ou \* (para todas as portas)|Tente utilizar intervalos de portas sempre que possível para evitar a necessidade de várias regras<br/>Várias portas ou intervalos de portas não podem ser agrupados por uma vírgula
|**Prefixo do endereço de origem**|Prefixo do endereço de origem ou etiqueta a corresponder para a regra|Endereço IP único (ou seja, 10.10.10.10), sub-rede IP (ou seja, 192.168.1.0/24), [etiqueta predefinida](#default-tags) ou * (para todos os endereços)|Considere a utilização de intervalos, etiquetas predefinidas e * para reduzir o número de regras|
|**Prefixo do endereço de destino**|Prefixo do endereço de destino ou etiqueta a corresponder para a regra|Endereço IP único (ou seja, 10.10.10.10), sub-rede IP (ou seja, 192.168.1.0/24), [etiqueta predefinida](#default-tags) ou * (para todos os endereços)|Considere a utilização de intervalos, etiquetas predefinidas e * para reduzir o número de regras|
|**Direção**|Direção do tráfego a corresponder para a regra|De entrada ou de saída|As regras de entrada e de saída são processadas em separado, com base na direção|
|**Prioridade**|As regras são verificados por ordem de prioridade; assim que uma regra é aplicada, não são testadas mais regras para correspondência|Número entre 100 e 4096|Considere a criação de regras que saltem as prioridades em 100 para cada regra, para deixar espaço para a entrada de novas regras entre as regras existentes|
|**Acesso**|Tipo de acesso a aplicar se a regra corresponder|Permitir ou negar|Tenha em atenção que se uma regra de permissão não for encontrada para um pacote, o pacote é ignorado|

Os NSGs contêm dois conjuntos de regras: de entrada e de saída. A prioridade para uma regra tem de ser exclusiva dentro de cada conjunto. 

![Processamento de regras do NSG](./media/virtual-network-nsg-overview/figure3.png) 

A figura acima mostra como as regras do NSG são processadas.

### Etiquetas Predefinidas

Etiquetas predefinidas são identificadores fornecidos pelo sistema para abordar uma categoria de endereços IP. Pode utilizar etiquetas predefinidas nas propriedades **prefixo do endereço de origem** e **prefixo do endereço de destino** de qualquer regra. Existem três etiquetas predefinidas que pode utilizar.

- **VIRTUAL_NETWORK:** esta etiqueta predefinida indica todos os espaços de endereços de rede. Inclui o espaço de endereços de rede virtual (intervalos CIDR definidos no Azure), bem como todos os espaços de endereços no local ligados e as VNets do Azure ligadas (redes locais).

- **AZURE_LOADBALANCER:** esta etiqueta predefinida indica o balanceador de carga da infraestrutura do Azure. Isto traduzir-se-á num IP de datacenter do Azure onde as sondas de estado de funcionamento do Azure têm origem.

- **INTERNET:** esta etiqueta predefinida indica o espaço de endereços IP que está fora da rede virtual e acessível pela Internet pública. Este intervalo também inclui o [espaço de IP público pertencente ao Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Regras Predefinidas

Todos os NSGs contêm um conjunto de regras predefinidas. As regras predefinidas não podem ser eliminadas, mas como lhes é atribuída a prioridade mais baixa, podem ser substituídas pelas regras que criar. 

Conforme ilustrado pelas regras predefinidas abaixo, o tráfego que tem origem e termina numa rede virtual é permitido nas direções de entrada e de saída. Embora a conectividade à Internet seja permitida para a direção de saída, está bloqueada por predefinição para a direção de entrada. Existe uma regra predefinida para permitir que o balanceador de carga do Azure sonde o estado de funcionamento das VMs e das instâncias de função. Pode substituir esta regra, caso não esteja a utilizar um conjunto com balanceamento de carga.

**Regras predefinidas de entrada**

| Nome                              | Prioridade | IP de origem          | Porta de origem | ID de destino  | Porta de destino | Protocolo | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| PERMITIR ENTRADA DE VNET                | 65000    | VIRTUAL_NETWORK    | *           | VIRTUAL_NETWORK | *                | *        | PERMITIR  |
| PERMITIR ENTRADA DO AZURE LOAD BALANCER | 65001    | AZURE_LOADBALANCER | *           | *               | *                | *        | PERMITIR  |
| NEGAR TODA A ENTRADA                  | 65500    | *                  | *           | *               | *                | *        | NEGAR   |

**Regras predefinidas de saída**

| Nome                    | Prioridade | IP de origem       | Porta de origem | ID de destino  | Porta de destino | Protocolo | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| PERMITIR SAÍDA DE VNET     | 65000    | VIRTUAL_NETWORK | *           | VIRTUAL_NETWORK | *                | *        | PERMITIR  |
| PERMITIR SAÍDA DE INTERNET | 65001    | *               | *           | INTERNET        | *                | *        | PERMITIR  |
| NEGAR TODA A SAÍDA       | 65500    | *               | *           | *               | *                | *        | NEGAR   |

## Associar NSGs

Pode associar um NSG a VMs, NICs e sub-redes, dependendo do modelo de implementação que estiver a utilizar.

[AZURE.INCLUDE [learn-about-deployment-models-both-include.md](../../includes/learn-about-deployment-models-both-include.md)]
 
- **Associar um NSG a uma VM (apenas implementações clássicas).** Quando associa um NSG a uma VM, as regras de acesso à rede no NSG são aplicadas a todo o tráfego destinado à VM e que sai da VM. 

- **Associar um NSG a um NIC (apenas implementações do Resource Manager).** Quando associa um NSG a um NIC, as regras de acesso à rede no NSG só são aplicadas a esse NIC. Isto significa que numa VM de vários NICs, se um NSG é aplicado a um único NIC, não afeta a tráfego vinculado a outros NICs. 

- **Associar um NSG a uma sub-rede (todas as implementações)**. Quando associa um NSG a uma sub-rede, as regras de acesso à rede no NSG são aplicadas a todos os recursos IaaS e PaaS na sub-rede. 

Pode associar NSGs diferentes a uma VM (ou NIC, consoante o modelo de implementação) e à sub-rede à qual um NIC ou uma VM está vinculada. Quando isso acontece, todas as regras de acesso à rede são aplicadas ao tráfego, por prioridade em cada NSG, pela seguinte ordem:

- **Tráfego de entrada**
    1. NSG aplicado a sub-rede. 
    
           If subnet NSG has a matching rule to deny traffic, packet will be dropped here.
    2. NSG aplicado a NIC (Resource Manager) ou VM (clássica). 
       
           If VM\NIC NSG has a matching rule to deny traffic, packet will be dropped at VM\NIC, although subnet NSG has a matching rule to allow traffic.
- **Tráfego de saída**
    1. NSG aplicado a NIC (Resource Manager) ou VM (clássica). 
      
           If VM\NIC NSG has a matching rule to deny traffic, packet will be dropped here.
    2. NSG aplicado a sub-rede.
       
           If subnet NSG has a matching rule to deny traffic, packet will be dropped here, although VM\NIC NSG has a matching rule to allow traffic.

    ![ACLs do NSG](./media/virtual-network-nsg-overview/figure2.png)

>[AZURE.NOTE] Embora só possa associar um único NSG a uma sub-rede, VM ou NIC, pode associar o mesmo NSG à quantidade de recursos que pretender.

## Implementação
Pode implementar NSGs nos modelos de implementação clássica e do Resource Manager com as diferentes ferramentas indicadas abaixo.

|Ferramenta de implementação|Clássica|Resource Manager|
|---|---|---|
|Portal clássico|![Não](./media/virtual-network-nsg-overview/red.png)|![Não](./media/virtual-network-nsg-overview/red.png)|
|Portal do Azure|![Sim](./media/virtual-network-nsg-overview/green.png)|[![Sim][verde]](virtual-networks-create-nsg-arm-pportal.md)|
|PowerShell|[![Sim][verde]](virtual-networks-create-nsg-classic-ps.md)|[![Sim][verde]](virtual-networks-create-nsg-arm-ps.md)|
|CLI do Azure|[![Sim][verde]](virtual-networks-create-nsg-classic-cli.md)|[![Sim][verde]](virtual-networks-create-nsg-arm-cli.md)|
|Modelo ARM|![Não](./media/virtual-network-nsg-overview/red.png)|[![Sim][verde]](virtual-networks-create-nsg-arm-template.md)|

|**Chave**|![Sim](./media/virtual-network-nsg-overview/green.png) Suportado.|![Não](./media/virtual-network-nsg-overview/red.png) Não suportado.|
|---|---|---|

## Planeamento

Antes de implementar os NSGs, terá de responder às perguntas abaixo:   

1. Para e a partir de que tipos de recursos pretende filtrar o tráfego (NICs na mesma VM, VMs ou outros recursos, tais como serviços em nuvem ou ambientes de serviço de aplicação ligados à mesma sub-rede ou entre recursos ligados a sub-redes diferentes)?

2. Os recursos para e a partir dos quais pretende filtrar o tráfego estão ligados a sub-redes em VNets existentes ou serão ligados a novas VNets ou sub-redes?
 
Para obter mais informações sobre o planeamento da segurança de rede no Azure, leia as [melhores práticas para serviços em nuvem e segurança de rede](../best-practices-network-security.md). 

## Considerações de conceção

Quando souber as respostas às perguntas na secção [Planeamento](#Planning), reveja o seguinte antes de definidos os seus NSGs.

### Limites

Tem de ter em consideração os seguintes limites durante a conceção dos seus NSGs.

|**Descrição**|**Limite Predefinido**|**Implicações**|
|---|---|---|
|Número de NSGs que pode associar a uma sub-rede, VM ou NIC|1|Isto significa que não é possível combinar NSGs. Certifique-se de que todas as regras necessárias para um determinado conjunto de recursos estão incluídas num único NSG.|
|NSGs por região por subscrição|100|Por predefinição, é criado um novo NSG para cada VM que criar no portal do Azure. Se permitir este comportamento predefinido, irá ficar rapidamente sem NSGs. Certifique-se de que tem este limite em atenção durante a conceção e separe os seus recursos por várias regiões ou subscrições, se necessário. |
|Regras do NSG por NSG|200|Utilize um intervalo amplo de IP e portas para garantir que não excede este limite. |

>[AZURE.IMPORTANT] Certifique-se de que consulta todos os [limites relacionados com serviços de redes no Azure](../azure-subscription-service-limits.md#networking-limits) antes de conceber a solução. Alguns limites podem ser aumentados ao abrir um pedido de suporte.

### Conceção de VNet e sub-rede

Uma vez que os NSGs podem ser aplicados a sub-redes, pode minimizar o número de NSGs agrupando os recursos por sub-rede e aplicando os NSGs a sub-redes.  Se optar por aplicar NSGs a sub-redes, pode achar que as VNets e sub-redes existentes que tem não foram definidas a pensar nos NSGs. Poderá ter de definir novas VNets e sub-redes para suportar a estrutura do NSG. Além disso, implemente os novos recursos nas novas sub-redes. Em seguida, pode definir uma estratégia de migração para mover os recursos existentes para as novas sub-redes. 

### Regras especiais

Tem de ter em conta as regras especiais indicadas abaixo. Certifique-se de que não bloqueia o tráfego permitido por essas regras; caso contrário, a sua infraestrutura não conseguirá comunicar com serviços essenciais do Azure.

- **IP Virtual do Nó de Anfitrião:** os serviços de infraestrutura básica, tais como DHCP, DNS e monitorização do estado de funcionamento são fornecidos através do endereço IP do anfitrião virtualizado 168.63.129.16. Este endereço IP público pertence à Microsoft e será o único endereço IP virtualizado utilizado em todas as regiões para este fim. Este endereço IP é mapeado para o endereço IP físico do computador servidor (nó de anfitrião) que está a alojar a máquina virtual. O nó de anfitrião funciona como o reencaminhamento DHCP, o resolvedor recursivo de DNS e a origem de sonda para a sonda de estado de funcionamento do balanceador de carga e a sonda de estado de funcionamento da máquina. A comunicação para este endereço IP não deve ser considerada um ataque.

- **Licenciamento (Key Management Service):** as imagens do Windows em execução nas máquinas virtuais devem ser licenciadas. Para tal, é enviado um pedido de licenciamento para os servidores de anfitrião do Key Management Service que processam estas consultas. Isto ocorrerá sempre na porta de saída 1688.

### Tráfego ICMP

As atuais regras do NSG admitem apenas protocolos *TCP* ou *UDP*. Não existe uma etiqueta específica para *ICMP*. No entanto, o tráfego ICMP é permitido dentro de uma Rede Virtual por predefinição através da regra VNet de entrada (regra predefinida, entrada 65000) que permite tráfego de/para qualquer porta e protocolo na VNet.

### Sub-redes

- Considere o número de camadas de que sua carga de trabalho necessita. Cada camada pode ser isolada através da utilização de uma sub-rede, com um NSG aplicado à sub-rede. 
- Se precisar de implementar uma sub-rede para um gateway de VPN ou circuito ExpressRoute, certifique-se de que **NÃO** aplica um NSG a essa sub-rede. Se o fizer, a conectividade em vários locais ou em várias VNets não funcionará.
- Se precisar de implementar uma aplicação virtual, certifique-se de que implementa a aplicação virtual na sua própria sub-rede, para que as Rotas Definidas pelo Utilizador (UDRs) possam funcionar corretamente. Pode implementar um NSG de nível de sub-rede para filtrar o tráfego que entra e sai desta sub-rede. Saiba mais sobre [como controlar o fluxo de tráfego e utilizar aplicações virtuais](virtual-networks-udr-overview.md).

### Balanceadores de carga

- Considere as regas de balanceamento de carga e de NAT para cada balanceador de carga que estiver a ser utilizado por cada uma das suas cargas de trabalho. Estas regras estão vinculadas a um conjunto de back-end que contém NICs (implementações do Resource Manager) ou VMs/instâncias de função (implementações clássicas). Considere criar um NSG para cada conjunto de back-end, permitindo apenas tráfego mapeado através das regras implementadas nos balanceadores de carga. Tal garante que o tráfego que vai diretamente para o conjunto de back-end, sem passar pelo balanceador de carga, também é filtrado.
- Em implementações clássicas, o utilizador cria pontos finais que mapeiam portas num balanceador de carga para portas nas VMs ou instâncias de função. Também pode criar o seu próprio balanceador de carga individual destinado ao público numa implementação do Resource Manager. Se estiver a restringir tráfego para VMs e instâncias de função que fazem parte de um conjunto de back-end num balanceador de carga utilizando NSGs, tenha em atenção que a porta de destino para o tráfego de entrada é a porta real na VM ou instância de função e não a porta exposta pelo balanceador de carga. Tenha também em atenção que a porta e o endereço de origem para a ligação à VM correspondem a uma porta e a um endereço no computador remoto na Internet e não à porta e ao endereço expostos pelo balanceador de carga.
- Tal como acontece com os balanceadores de carga destinados ao público, quando cria NSGs para filtrar o tráfego que vem de um balanceador de carga interno (ILB), é necessário compreender que o intervalo de endereços e portas de origem aplicados são os do computador onde a chamada tem origem e não do balanceador de carga. Além disso, o intervalo de endereços e de portas de destino estão relacionados com o computador que recebe o tráfego e não com o balanceador de carga.

### Outros

- As ACLs baseadas em ponto final e os NSGs não são suportados na mesma instância de VM. Se pretender utilizar um NSG e ter uma ACL de ponto final já implementada, remova primeiro a ACL de ponto final. Para obter informações sobre como fazê-lo, consulte o artigo [Gerir ACLs de ponto final](virtual-networks-acl-powershell.md).
- No modelo de implementação do Resource Manager, pode utilizar um NSG associado a um NIC para VMs com vários NICs para ativar a gestão (acesso remoto) por NIC, separando assim o tráfego.
- Tal como acontece com a utilização de balanceadores de carga, ao filtrar o tráfego de outras VNets, tem de utilizar o intervalo de endereços de origem do computador remoto e não o gateway que liga as VNets.
- Muitos serviços do Azure não podem ser ligados a Redes Virtuais do Azure e, por isso, o tráfego de e para os mesmos não pode ser filtrado com NSGs.  Leia a documentação relativa aos serviços que utilizar para determinar se podem ou não ser ligados a VNets.

## Implementação de exemplo

Para ilustrar a aplicação das informações deste artigo, iremos definir os NSGs para filtrar o tráfego de rede para uma solução de carga de trabalho de duas camadas com os seguintes requisitos:

1. Separação do tráfego entre front- end (Windows Web Servers) e back-end (servidores da base de dados SQL).
2. Regras de balanceamento de carga que reencaminham o tráfego para o balanceador de carga para todos os servidores web na porta 80.
3. Regras NAT que reencaminham o tráfego que entra na porta 50001 no balanceador de carga para a porta 3389 em apenas uma VM no front-end.
4. Sem acesso às VMs de front-end ou back-end a partir da Internet, com exceção do número de requisito 1.
5. Sem acesso a partir do front-end ou back-end à Internet.
6. Acesso à porta 3389 para qualquer servidor Web no front-end, para o tráfego proveniente da própria sub-rede do front-end.
7. Acesso à porta 3389 para todas as VMs de SQL Server no back-end a partir apenas da sub-rede do front-end.
8. Acesso à porta 1433 para todas as VMs de SQL Server no back-end a partir apenas da sub-rede do front-end.
9. Separação do tráfego de gestão (porta 3389) e do tráfego de base de dados (1433) em diferentes NICs nas VMs do back-end.

![NSGs](./media/virtual-network-nsg-overview/figure1.png)

Tal como ilustrado no diagrama acima, as VMs *Web1* e *Web2* estão ligadas à sub-rede *FrontEnd*, e as VMs *DB1* e *DB2* estão ligadas à sub-rede *BackEnd*.  Ambas as sub-redes fazem parte da VNet *TestVNet*. Todos os recursos são atribuídos à região do Azure *E.U.A. Oeste*.

Os requisitos 1-6 (à exceção do 3) acima estão todos limitados a espaços de sub-rede. Para minimizar o número de regras necessárias para cada NSG e para facilitar a adição de mais VMs às sub-redes que executam os mesmos tipos de cargas de trabalho que as VMs existentes, pode implementar os seguintes  NSGs de nível de sub-rede.

### NSG para sub-rede FrontEnd

**Regras de entrada**

|Regra|Access|Prioridade|Intervalo de endereços de origem|Porta de origem|Intervalo de endereços de destino|Porta de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|permitir HTTP|Permitir|100|INTERNET|\*|\*|80|TCP|
|permitir RDP de FrontEnd|Permitir|200|192.168.1.0/24|\*|\*|3389|TCP|
|negar tudo da Internet|Negar|300|INTERNET|\*|\*|\*|TCP|

**Regras de saída**

|Regra|Access|Prioridade|Intervalo de endereços de origem|Porta de origem|Intervalo de endereços de destino|Porta de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|negar Internet|Negar|100|\*|\*|INTERNET|\*|\*|

### NSG para sub-rede de back-end

**Regras de entrada**

|Regra|Access|Prioridade|Intervalo de endereços de origem|Porta de origem|Intervalo de endereços de destino|Porta de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|negar Internet|Negar|100|INTERNET|\*|\*|\*|\*|

**Regras de saída**

|Regra|Access|Prioridade|Intervalo de endereços de origem|Porta de origem|Intervalo de endereços de destino|Porta de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|negar Internet|Negar|100|\*|\*|INTERNET|\*|\*|

### NSG para VM única (NIC) no front-end para RDP a partir da Internet

**Regras de entrada**

|Regra|Access|Prioridade|Intervalo de endereços de origem|Porta de origem|Intervalo de endereços de destino|Porta de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|permitir RDP da Internet|Permitir|100|INTERNET|*|\*|3389|TCP|

>[AZURE.NOTE] Repare como o intervalo de endereços de origem para esta regra é **Internet** e não o VIP do balanceador de carga; a porta de origem é **\*** e não 500001. Não confunda regras de NAT/regras de balanceamento de carga com regras de NSG. As regras de NSG estão sempre relacionadas com a origem original e o destino final do tráfego, e **NÃO** com o balanceador de carga entre os dois. 

### NSG para NICs de gestão em back-end

**Regras de entrada**

|Regra|Access|Prioridade|Intervalo de endereços de origem|Porta de origem|Intervalo de endereços de destino|Porta de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|permitir RDP de front-end|Permitir|100|192.168.1.0/24|*|\*|3389|TCP|

### NSG para NICs de acesso à base de dados em back-end

**Regras de entrada**

|Regra|Access|Prioridade|Intervalo de endereços de origem|Porta de origem|Intervalo de endereços de destino|Porta de destino|Protocolo|
|---|---|---|---|---|---|---|---|
|permitir SQL de front-end|Permitir|100|192.168.1.0/24|*|\*|1433|TCP|

Uma vez que alguns dos NSGs acima têm de ser associados a NICs individuais, tem de implementar este cenário como uma implementação do Resource Manager. Repare como as regras são combinadas para o nível de sub-rede e de NIC, dependendo de como têm de ser aplicadas. 

## Passos seguintes

- [Implementar NSGs no modelo de implementação clássica](virtual-networks-create-nsg-classic-ps.md).
- [Implementar NSGs no Resource Manager](virtual-networks-create-nsg-arm-pportal.md).
- [Gerir registos de NSG](virtual-network-nsg-manage-log.md).

[verde]: ./media/virtual-network-nsg-overview/green.png
[yellow]: ./media/virtual-network-nsg-overview/yellow.png
[red]: ./media/virtual-network-nsg-overview/red.png



<!--HONumber=Sep16_HO3-->


