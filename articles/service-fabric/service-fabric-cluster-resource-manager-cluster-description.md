---
title: "Descrição do Cluster de Gestor de recursos do cluster | Microsoft Docs"
description: "Que descrevem um cluster do Service Fabric, especificando os domínios de falhas, domínios de atualização, propriedades de nó e as capacidades de nó para o Gestor de recursos do Cluster."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 26ce9e96dd4df170e80c2c61dcc08c70357eec22
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="describing-a-service-fabric-cluster"></a>Que descrevem um cluster do service fabric
O Gestor de recursos de Cluster do Service Fabric fornece vários mecanismos para descrever um cluster. Durante o tempo de execução, o Gestor de recursos de Cluster utiliza estas informações para assegurar elevada disponibilidade dos serviços em execução no cluster. Ao impor estas regras importantes, este também tenta otimizar o consumo de recursos do cluster.

## <a name="key-concepts"></a>Conceitos-chave
O Gestor de recursos de Cluster suporta várias funcionalidades que descrevem um cluster:

* Domínios de falhas
* Domínios de atualização
* Propriedades de nó
* Capacidades de nó

## <a name="fault-domains"></a>Domínios de falha
Um domínio de falhas é qualquer área da falha coordenada. Uma única máquina é um domínio de falhas (uma vez que pode efetuar a no seus próprio para várias razões, de falhas de alimentação de energia a falhas de unidade ao firmware NIC incorreto). As máquinas ligadas ao mesmo comutador Ethernet estão no mesmo domínio de falhas, como máquinas partilhar uma única origem de energia ou numa única localização. Uma vez que é natural para falhas de hardware sobrepor, domínios de falhas são inerentemente hierárquica e são representados como os URIs no Service Fabric.

É importante que domínios de falhas estão corretamente configuradas, uma vez que o Service Fabric utiliza estas informações para colocar em segurança serviços. Service Fabric não pretende colocar os serviços de forma a que a perda de um domínio de falhas (causado pela falha de alguns componentes) faz com que um serviço abaixo. O Azure ambiente Service Fabric utiliza as informações de domínio de falhas fornecidas pelo ambiente para configurar corretamente os nós do cluster em seu nome. Autónomo do serviço de recursos de infraestrutura, domínios de falhas são definidos no momento em que o cluster está configurado 

> [!WARNING]
> É importante que as informações de domínio de falhas fornecidas para o serviço de recursos de infraestrutura estão corretas. Por exemplo, digamos que nós do cluster do Service Fabric estão em execução dentro de 10 máquinas virtuais, em execução em cinco anfitriões físicos. Neste caso, apesar de existirem 10 máquinas virtuais, existem apenas 5 diferente (nível superior) domínios de falha. Partilhar o mesmo anfitrião físico, faz com que as VMs partilhar o mesmo domínio de falhas de raiz, uma vez que as VMs experiência coordenada falha se falhar o respetivo anfitrião físico.  
>
> Service Fabric espera que o domínio de falhas de um nó não para alterar. Outros mecanismos de garantir a elevada disponibilidade das VMs, tais como [HA-VMs](https://technet.microsoft.com/en-us/library/cc967323.aspx) pode fazer com que está em conflito com o Service Fabric, como que utilizam migração transparente de VMs a partir de um anfitrião para outro. Estes mecanismos não reconfigurar ou notificar o código em execução dentro da VM. Como tal, estão **não suportado** como ambientes para executar o Service Fabric clusters. Recursos de infraestrutura de serviço deve ser utilizada a tecnologia de apenas elevada disponibilidade. Mecanismos, como migração em direto de VM, SAN, ou outras não são necessárias. Se utilizado em conjunto com o Service Fabric, estes mecanismos _reduzir_ fiabilidade e disponibilidade de aplicações porque estes introduzem complexidade adicional, adicione origens centralizadas de falha e utilizar a fiabilidade e estratégias de disponibilidade que estão em conflito com os recursos de infraestrutura de serviço. 
>
>

No gráfico abaixo, iremos cor todas as entidades que listam todos os a diferentes domínios de falhas que resultam e contribuem para domínios de falhas. Neste exemplo, temos de centros de dados ("DC"), bastidores ("R") e painéis ("B"). Pode, se cada painel contém mais do que uma máquina virtual, pode existir outra camada na hierarquia de domínio de falhas.

<center>
![Nós organizados através de domínios de falhas][Image1]
</center>

Durante o tempo de execução, o Gestor de recursos de Cluster do Service Fabric considera os domínios de falhas do cluster e planos de esquemas. As réplicas com monitorização de estado ou instâncias sem monitorização de estado para um determinado serviço são distribuídas para que estejam em domínios de falhas separados. Distribui o serviço em domínios de falhas garante que a disponibilidade do serviço não for comprometida, quando um domínio de falhas falhar em qualquer nível da hierarquia.

Gestor de recursos de Cluster do Service Fabric não está interessado quantas camadas existem na hierarquia de domínio de falhas. No entanto, este tenta Certifique-se de que a perda de qualquer uma parte da hierarquia não afeta a serviços em execução no mesmo. 

Recomenda-se existirem o mesmo número de nós a cada nível de profundidade da hierarquia de domínio de falhas. Se a "árvore" de domínios de falhas é desequilibrada no seu cluster, este torna mais difícil para o Gestor de recursos do Cluster para descobrir a alocação de procedimento dos serviços. Esquemas de domínios de falhas imbalanced significam que a perda de algumas impacto de domínios a disponibilidade dos serviços mais do que outros domínios. Como resultado, o Gestor de recursos do Cluster é dividido entre dois objetivos: pretende utilizar as máquinas no domínio "pesado" colocando serviços nos mesmos e pretende colocar os serviços de outros domínios para que a perda de um domínio não causa problemas. 

O que o aspeto de domínios imbalanced? No diagrama abaixo, mostramos dois esquemas de outro cluster. No primeiro exemplo, os nós são distribuídos uniformemente os domínios de falhas. O segundo exemplo, um domínio de falhas tem muitas mais nós que outros domínios de falhas. 

<center>
![Esquemas de outro cluster de dois][Image2]
</center>

No Azure, a escolha de que o domínio de falhas contém um nó é gerida por si. No entanto, dependendo do número de nós que for aprovisionado pode ainda acaba por ficar com domínios de falhas com mais nós nas mesmas que outros. Por exemplo, imagine tiver cinco domínios de falhas no cluster, mas aprovisionar sete nós para um NodeType indicado. Neste caso, o primeiro dois domínios de falhas acaba por ficar com mais nós. Se continuar a implementar mais NodeTypes com apenas algumas instâncias, o problema obtém um. Por este motivo, que é recomendado que o número de nós em cada nó do tipo é um múltiplo do número de domínios de falhas.

## <a name="upgrade-domains"></a>Domínios de atualização
Domínios de atualização são outra funcionalidade que ajuda o Gestor de recursos do Cluster do Service Fabric compreender o esquema do cluster. Domínios de atualização definem conjuntos de nós que são atualizadas ao mesmo tempo. Domínios de atualização ajudam o Gestor de recursos de Cluster, compreender e orquestrar com operações de gestão, como atualizações.

Domínios de atualização são muito como domínios de falhas, mas com algumas diferenças principais. Em primeiro lugar, áreas de falhas de hardware coordenada definem domínios de falhas. Por outro lado, os domínios de atualização, são definidos pela política. Introdução ao decidir quantos quiser, em vez de-lo a ser ditado pelo ambiente. Pode ter tantos domínios atualizar tal nós. Outra diferença entre domínios de falhas e domínios de atualização é que os domínios de atualização não são hierárquicos. Em vez disso, são mais como uma etiqueta simple. 

O diagrama seguinte mostra que três domínios de atualização estão repartidos em três domínios de falhas. Também mostra uma possível posicionamento para três réplicas de diferentes de um serviço com monitorização de estado, onde cada termina em diferentes falhas e domínios de atualização. Esta colocação permite a perda de um domínio de falhas ao meio de uma atualização de serviço e continuará a ter uma cópia de código e aos dados.  

<center>
![Posicionamento com falhas e domínios de atualização][Image3]
</center>

Existem os profissionais de TI e contras ao facto de grandes quantidades de domínios de atualização. Domínios de atualização mais significa cada passo da atualização é mais granular e, por conseguinte, afeta um número menor de nós ou serviços. Como resultado, menos os serviços tem de mover cada vez, introduzindo o menor volume de alterações no sistema. Isto tende a melhorar a fiabilidade, uma vez que o menor de serviço é afetado por qualquer problema que introduziu durante a atualização. Domínios de atualização mais também significa que terá de memória intermédia inferior disponível nos outros nós para processar o impacto da atualização. Por exemplo, se tiver cinco domínios de atualização, os nós em cada são processar aproximadamente 20% do tráfego. Se precisar de tirá esse domínio de atualização para uma atualização, que a carga, normalmente, tem de ir algures. Uma vez que tem quatro domínios restantes atualizar, cada um tem de ter espaço para cerca de 5% do tráfego total. Domínios de atualização mais significa que terá de menos memória intermédia em nós no cluster. Por exemplo, considere se tinha 10 domínios de atualização. Nesse caso, cada UD seria processamento apenas cerca de 10% do tráfego total. Quando um passos de atualização através do cluster, cada domínio seria só tem de ter espaço para cerca de 1.1% do tráfego total. Domínios de atualização mais geralmente permitem-lhe executar os nós de numa maior utilização, uma vez que precisar de capacidade de menos reservada. O mesmo se aplica para domínios de falhas.  

Downside de ter vários domínios de atualização é que as atualizações tendem a demorar mais tempo. Service Fabric aguarda um curto período de tempo após a conclusão de um domínio de atualização e efetua verificações antes de iniciar a atualização dos seguinte. Estes atrasos de ativar a detetar problemas introduzidos pela atualização antes de continua a atualização. O compromisso é aceitável, porque impede que as alterações incorretas afetar demasiada do serviço de cada vez.

Domínios de atualização de poucos tem muitas efeitos secundários negativos – apesar de cada domínio de atualização individual está inativo e a ser atualizado uma grande parte da sua capacidade global está indisponível. Por exemplo, se tiver apenas três domínios de atualização que está a efetuar para baixo cerca de 1/3 do seu serviço geral ou a capacidade de cluster num momento. Ter, pelo que, muito do seu serviço para baixo em simultâneo não é desejável, uma vez que tem de ter capacidade suficiente no resto do seu cluster para processar a carga de trabalho. Manter essa memória intermédia significa que durante o funcionamento normal os nós estão inferior carregar do que seria, caso contrário. Isto aumenta o custo de execução do seu serviço.

Não há nenhum limite real para o número total de falhas ou domínios de atualização no ambiente ou restrições em como sobrepor. Dito isto, existem alguns padrões comuns:

- Atualizar domínios e de domínios de falhas mapeado 1:1
- Um domínio de atualização de mensagens em fila por nó (instância de SO física ou virtual)
- Um modelo de "repartido" ou "matriz" onde os domínios de falhas e os domínios de atualização formam uma matriz com máquinas normalmente em execução para baixo as diagonals

<center>
![Índice de falhas e esquemas de domínio de atualização][Image4]
</center>

Não há que nenhum melhor o esquema a escolha de responder, cada um tem algumas os profissionais de TI e contras. Por exemplo, o modelo de 1FD:1UD é simple de configurar. O 1 o domínio de atualização por modelo de nó é a maioria dos, como as pessoas são utilizadas para. Durante as atualizações de cada nó é atualizado independentemente. Isto é semelhante à forma como pequeno conjuntos de máquinas foram atualizados manualmente no passado. 

O modelo mais comuns é a matriz de DF/UD, onde o FDs e UDs formam uma tabela e nós são colocados iniciar ao longo de diagonal. Este é o modelo utilizado por predefinição em clusters de Service Fabric no Azure. Para clusters com vários nós tudo acaba por ficar procura, como o padrão de matriz em acima.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restrições de falhas e de domínio de atualização e o comportamento resultante
O Gestor de recursos de Cluster trata a intenção de manter um serviço equilibrado nos domínios de atualização como uma restrição e de falhas. Pode encontrar mais informações sobre restrições em [neste artigo](service-fabric-cluster-resource-manager-management-integration.md). O estado de restrições de falhas e de domínio de atualização: "para uma partição de serviço especificado não existe nunca devem ser uma diferença *maior do que um* no número de objetos do serviço (instâncias de serviço sem monitorização de estado ou réplicas com monitorização de estado do serviço) entre dois domínios." Isto impede que determinadas move ou disposições que violam esta restrição.

Vamos ver um exemplo. Imaginemos que temos um cluster com seis nós, configurados com cinco domínios de falhas e cinco domínios de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Agora vamos supor que criamos um serviço com um TargetReplicaSetSize (ou, para um serviço sem estado um InstanceCount) cinco. As réplicas apresentado no N1 N5. Na verdade, N6 nunca é utilizada, independentemente de quantas serviços assim que cria. Mas, por que motivo? Vamos ver a diferença entre o esquema atual e o que aconteceria se N6 é escolhido.

Eis o esquema obtivemos e o número total de réplicas por falhas e de domínio de atualização:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este esquema é balanceado em termos de nós por domínio de falhas e de domínio de atualização. É também com balanceamento em termos do número de réplicas por falhas e de domínio de atualização. Cada domínio tem o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver que aconteceria se tinha utilizámos N6 em vez de N2. Forma como iriam as réplicas ser distribuídas, em seguida?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Este esquema viola o nosso definição para a restrição de domínio de falhas. FD0 tem duas réplicas, enquanto FD1 tem zero, tornando a diferença entre FD0 e FD1 um total de dois. O Gestor de recursos de Cluster não permite esta disposição. Da mesma forma se podemos selecionado N2 e N6 (em vez de N1 e N2) seria obtemos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este esquema é balanceado em termos de domínios de falhas. No entanto, agora, está violação de restrição de domínio de atualização. Isto acontece porque UD0 tem zero réplicas enquanto UD1 tem dois. Por conseguinte, este esquema também é inválido e não ser selecionado pelo Gestor de recursos de Cluster. 

## <a name="configuring-fault-and-upgrade-domains"></a>Configurar e atualizar domínios de falhas
Definir domínios de falhas e domínios de atualização é feito automaticamente no Azure alojadas implementações de Service Fabric. Service Fabric seleciona e utiliza as informações de ambiente do Azure.

Se estiver a criar o seu próprio cluster (ou pretende executar uma topologia específica em desenvolvimento), pode fornecer as informações de domínio de falhas e de domínio de atualização. Neste exemplo, iremos definir um cluster de desenvolvimento local do nove nó que abranja três "centros de dados" (cada um com três bastidores). Este cluster tem também três domínios de atualização repartidos por esses três centros de dados. Abaixo é um exemplo da configuração: 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

através de Clusterconfig para implementações autónomas

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Quando definir clusters através do Azure Resource Manager, os domínios de falhas e domínios de atualização são atribuídos pelo Azure. Por conseguinte, a definição dos seus tipos de nó e conjuntos de dimensionamento de Máquina Virtual no modelo Azure Resource Manager não inclui informações de domínio de atualização ou domínio de falhas.
>

## <a name="node-properties-and-placement-constraints"></a>Restrições de posicionamento e propriedades de nó
Por vezes (no facto, a maioria das vezes) que vai pretende garantir que determinadas cargas de trabalho é executado apenas em determinados tipos de nós no cluster. Por exemplo, algumas cargas de trabalho podem requerer GPUs ou SSDs enquanto outros podem não. Um exemplo de uma grande de filtragem de hardware para cargas de trabalho específicas é quase todas as arquitetura de n camadas horizontalmente. Determinados máquinas servem como o front-end ou a API que serve o lado da aplicação e ficam expostas a clientes ou de internet. Vários computadores, muitas vezes, com os recursos de hardware diferente, processam o trabalho de camadas de armazenamento ou de computação. Normalmente, são _não_ expostos diretamente a clientes ou de internet. Service Fabric espera que há casos em que as cargas de trabalho específicas tem de executar em configurações de hardware específico. Por exemplo:

* uma aplicação de camada de n existente foi "lifted e desviado" para um ambiente do Service Fabric
* uma carga de trabalho que pretende executar no hardware específico para o desempenho, escala ou por motivos de isolamento de segurança
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por motivos de consumo de política ou de recursos

Para suportar estes tipos de configurações, o Service Fabric tem uma noção de primeira classe das etiquetas que pode ser aplicado a nós. Estas etiquetas são denominadas **propriedades de nó**. **Restrições de posicionamento** são as declarações ligadas a serviços individuais que selecionar para uma ou mais propriedades de nó. Restrições de posicionamento definem onde os serviços devem ser executados. O conjunto de restrições está extensível - qualquer par chave/valor pode trabalhar. 

<center>
![Cargas de trabalho diferente do esquema de cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Incorporadas nas propriedades de nó
Recursos de infraestrutura de serviço define algumas propriedades de nó predefinidas que podem ser utilizadas automaticamente sem o utilizador ter de defini-las. As propriedades predefinidas definidas em cada nó são o **NodeType** e **NodeName**. Por isso, por exemplo pode escrever uma restrição de posicionamento como `"(NodeType == NodeType03)"`. Tem, geralmente, encontrámos NodeType para ser um dos mais frequentemente utilizadas propriedades. Esta opção é útil porque corresponde à 1:1 com um tipo de uma máquina. Cada tipo de máquina corresponde a um tipo de carga de trabalho numa aplicação de camada de n tradicional.

<center>
![Restrições de posicionamento e propriedades de nó][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Restrição de posicionamento e a sintaxe de propriedade de nó 
O valor especificado na propriedade do nó pode ser uma cadeia, booleano, ou assinada longa. A instrução de serviço é chamada um posicionamento *restrição* uma vez que o se restringe onde o serviço pode ser executado no cluster. A restrição pode ser qualquer instrução booleana que opera nas propriedades de outro nó no cluster. Os seletores válidos nestas instruções booleanos são:

1) condicionais verificações para a criação de instruções específicas

| Instrução | Sintaxe |
| --- |:---:|
| "igual a" | "==" |
| "não é igual a" | "!=" |
| "superior" | ">" |
| "maior que ou igual a" | ">=" |
| "inferior" | "<" |
| "menor ou igual a" | "<=" |

2) instruções booleanos para operações de agrupamento e lógicas

| Instrução | Sintaxe |
| --- |:---:|
| "e" | "&&" |
| "ou" | "&#124;&#124;" |
| "não" | "!" |
| "grupo como instrução única" | "()" |

Seguem-se alguns exemplos de declarações de restrição básica.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Apenas nós em que a declaração de restrição de posicionamento geral avalia como "True" podem ter o serviço colocado na mesma. Nós que não tem uma propriedade definida não correspondem a qualquer restrição de posicionamento que contém essa propriedade.

Digamos que as seguintes propriedades de nó foram definidas para um tipo de nó especificado:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters. 

> [!NOTE]
> No modelo Azure Resource Manager o tipo de nó é normalmente parametrizado. -Teria aspeto "[parameters('vmNodeType1Name')]" em vez de "NodeType01".
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Pode criar o posicionamento do serviço *restrições* para um serviço como da seguinte forma:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se todos os nós do NodeType01 são válidos, também pode selecionar esse tipo de nó com a restrição "(NodeType == NodeType01)".

Uma das ações frios sobre restrições de posicionamento de um serviço é que pode ser atualizados dinamicamente durante o tempo de execução. Por isso, se necessário, pode mover um serviço em torno do cluster, adicionar e remover requisitos, etc. Service Fabric encarrega-se de garantir que o serviço permanece disponível e se mesmo quando são efetuados estes tipos de alterações.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Restrições de posicionamento são especificadas para cada instância de serviço com nome diferente. Sempre ocorrer atualizações a de (substituir) que foi anteriormente especificado.

A definição de cluster define as propriedades num nó. Alterar propriedades de um nó requer uma atualização da configuração de cluster. Atualizar propriedades de um nó necessita de cada nó afetado reiniciar para comunicar as respetivas propriedades de novo. Estes implementar as atualizações são geridas pelo Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Descrever e gerir recursos de Cluster
Uma das tarefas mais importantes de qualquer orchestrator é ajudar a gerir o consumo de recursos do cluster. Gerir recursos do cluster pode significar algumas ações diferentes. Em primeiro lugar, existe é garantir que as máquinas não estão sobrecarregadas. Isto significa certificando-se de que as máquinas não estão em execução mais serviços a que podem processar. Segundo, há balanceamento e a otimização de que é fundamental para executar os serviços de forma eficiente. Ofertas de serviço confidenciais custos eficaz ou de desempenho não podem permitir alguns nós frequente enquanto outros estão amovíveis. Nós frequente levar a contenção de recursos e o desempenho fraco e amovíveis nós representam wasted recursos e aumentar os custos. 

Service Fabric representa recursos como `Metrics`. As métricas são qualquer recurso lógico ou físico que pretende para descrever a Service Fabric. Exemplos de métricas são coisas como "WorkQueueDepth" ou "MemoryInMb". Para informações sobre os recursos físicos que podem reger Service Fabric em nós, consulte [governação de recursos](service-fabric-resource-governance.md). Para informações sobre como configurar métricas personalizadas e respetivas utilizações, consulte [neste artigo](service-fabric-cluster-resource-manager-metrics.md)

As métricas são diferentes das restrições de possam ser posicionadas livremente e propriedades de nó. Propriedades de nó são estáticos descritores dos próprios nós. Métricas descrevem os recursos que têm de nós e que serviços consumam quando são executados num nó. Uma propriedade de nó pode ser "HasSSD" e pode ser definida como VERDADEIRO ou FALSO. A quantidade de espaço disponível nesse SSD e quantidade consumida pelos serviços seria uma métrica como "DriveSpaceInMb". 

É importante ter em atenção que, tal como para restrições de posicionamento e propriedades de nó, o Gestor de recursos de Cluster do serviço de recursos de infraestrutura não compreender o que significa que os nomes das métricas. Os nomes de métricos são apenas cadeias. É uma boa prática para declarar unidades como parte dos nomes métricas que criar pode ser ambígua.

## <a name="capacity"></a>Capacidade
Caso tenha desativado todos os recursos *balanceamento*, Gestor de recursos de Cluster do Service Fabric seria ainda Certifique-se de que nenhum nó terminou cópias de segurança ao longo do respetiva capacidade. É possível gerir excessos de capacidade, a menos que o cluster é demasiado completo ou a carga de trabalho é maior do que qualquer nó. A capacidade é outra *restrição* que o Gestor de recursos de Cluster utiliza para compreender a quantidade de um recurso de um nó possui. Restante capacidade também está registado para o cluster como um todo. A capacidade e o consumo de nível de serviço são expressas em termos de métricas. Por isso, por exemplo, a métrica poderá ser "ClientConnections" e um determinado nó pode ter uma capacidade para "ClientConnections" de 32768. Outros nós podem ter outros limites algumas serviço em execução nesse nó pode diga-está atualmente a consumir 32256 da métrica "ClientConnections".

Durante o tempo de execução, o Gestor de recursos de Cluster controla a capacidade restante do cluster e em nós. Para poder controlar a capacidade do Gestor de recursos de Cluster subtrai utilização de cada serviço a partir da capacidade do nó onde o serviço seja executado. Com esta informação, o Gestor de recursos de Cluster do serviço de recursos de infraestrutura pode descobrir onde pretende colocar ou mover réplicas para que nós não passam a capacidade.

<center>
![Nós de cluster e a capacidade][Image7]
</center>

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Pode ver as capacidades definidas no manifesto do cluster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters. 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Normalmente um serviço carregar alterações dinamicamente. Imaginemos que a carga de uma réplica de "ClientConnections" foi alterado de 1024 2048, mas o nó que foi executada, em seguida, apenas tivesse capacidade 512 restantes para esse métrica. Agora essa réplica ou de posicionamento da instância é inválido, uma vez que não há espaço suficiente nesse nó. O Gestor de recursos do Cluster tem de iniciar e obter o nó volta abaixo capacidade. Reduz a carga no nó que é efetuada através de capacidade ao mover um ou mais das réplicas ou instâncias desse nó para outros nós. Quando move réplicas, o Gestor de recursos de Cluster tenta minimizar o custo desses movimentos. O custo de movimento é abordado em [neste artigo](service-fabric-cluster-resource-manager-movement-cost.md) e mais informações sobre o Gestor de recursos do Cluster do reequilíbrio estratégias e regras é descrito [aqui](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacidade de cluster
Como, por isso, o Gestor de recursos de Cluster do serviço de recursos de infraestrutura manter o cluster geral de ser demasiado completa? Assim, com carga dinâmico, não há muito pode fazer. Os serviços podem ter os respetivos pico de carga de pedidos independentemente as ações executadas pelo Gestor de recursos de Cluster. Como resultado, o cluster com muitos headroom hoje pode ser underpowered ficarem que famosa amanhã. Dito isto, existem alguns controlos que são integrados para evitar problemas. A primeira coisa que podemos fazer é evitar a criação de novas cargas de trabalho que faria com que o cluster para se tornarem completa.

Imaginemos que cria um serviço sem monitorização de estado e tem algumas carga associada. Imaginemos que o serviço cares sobre a métrica de "DiskSpaceInMb". Também Digamos que pretender consumir cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Pretende criar três instâncias do serviço. Ótimo! Por isso, que significa que precisamos 15 unidades de "DiskSpaceInMb" estar presente no cluster para que nos permitam até conseguir criar estas instâncias de serviço. O Gestor de recursos de Cluster continuamente calcula a capacidade e o consumo de cada métrica para que possa determinar a capacidade restante do cluster. Se não existir espaço suficiente, o Gestor de recursos de Cluster rejeita a chamada de serviço de criar.

Uma vez que o requisito de que só existe 15 unidades de estar disponível, este espaço foi alocado muitas formas diferentes. Por exemplo, pode existir uma unidade restantes da capacidade em 15 nós diferentes, ou três unidades restantes de capacidade em cinco nós diferentes. Se o Gestor de recursos do Cluster pode reorganizar coisas, pelo que não existe cinco unidades disponíveis em três nós, coloca o serviço. Reorganização de cluster é possível que, normalmente, a menos que o cluster está quase cheia ou os serviços existentes não podem ser consolidados por algum motivo.

## <a name="buffered-capacity"></a>Capacidade de memória intermédia
Capacidade de memória intermédia é outra funcionalidade do Gestor de recursos do Cluster. Permite a reserva de uma parte da capacidade global do nó. Esta memória intermédia de capacidade só é utilizada para colocar os serviços durante as atualizações e falhas de nó. Capacidade de memória intermédia é especificada globalmente por métrica para todos os nós. O valor que escolher para a capacidade reservada é uma função do número de falhas e domínios de atualização ter no cluster. Mais de falhas e de domínios de atualização significa que pode escolher um número inferior para a sua capacidade de memória intermédia. Se tiver mais domínios, que pode esperar mais pequenas quantidades de cluster fique indisponível durante as atualizações e falhas. Especificar a capacidade de colocação em memória intermédia apenas faz sentido se tiver especificado também a capacidade de nó para uma métrica.

Eis um exemplo de como especificar a capacidade de memória intermédia:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

A criação de novos serviços falha quando o cluster sem capacidade de memória intermédia para uma métrica. Impedir a criação de novos serviços para preservar a memória intermédia assegura que as atualizações e falhas não fazer com que nós para abordar a capacidade. Capacidade de memória intermédia é opcional, mas recomenda-se em qualquer cluster que define uma capacidade de uma métrica.

O Gestor de recursos de Cluster expõe estas informações de carga. Para cada métrica, estas informações incluem: 
  - as definições de capacidade de memória intermédia
  - a capacidade total
  - o consumo de atual
  - Indica se cada métrica é considerada balanceada ou não
  - estatísticas sobre o desvio-padrão
  - os nós que têm mais e menos de carga  
  
Abaixo, vemos um exemplo dessa saída:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Passos seguintes
* Para obter informações sobre o fluxo de informações e arquitetura dentro do Gestor de recursos do Cluster, consulte [neste artigo](service-fabric-cluster-resource-manager-architecture.md)
* Definir métricas de desfragmentação é uma forma para consolidar a carga em nós em vez de propagando-se este. Para saber como configurar a desfragmentação, consulte [neste artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Iniciar a partir do início e [obtenha uma introdução para o serviço de recursos de infraestrutura Cluster Gestor de recursos](service-fabric-cluster-resource-manager-introduction.md)
* Para obter informações sobre a forma como o Gestor de recursos de Cluster gere e equilibra a carga no cluster, consulte o artigo em [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
