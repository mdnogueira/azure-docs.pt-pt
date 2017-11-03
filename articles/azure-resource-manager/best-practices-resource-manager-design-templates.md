---
title: "Os modelos do Azure para soluções complexas de design | Microsoft Docs"
description: "Mostra as melhores práticas para criar modelos do Azure Resource Manager para cenários complexos"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ce1141d6-ece7-4976-acea-1db1f775409e
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
ms.openlocfilehash: dcc31f7a8c85a8f7fbd554371a66fb1e348bca17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="design-patterns-for-azure-resource-manager-templates-when-deploying-complex-solutions"></a>Padrões de estrutura de modelos Azure Resource Manager, ao implementar soluções complexas
Utilizar uma abordagem flexível com base nos modelos Azure Resource Manager, pode implementar topologias complexas rápida e consistentemente. Pode adaptar estas implementações facilmente como ofertas de núcleos evoluem ou para acomodar variantes para cenários de valores atípicos ou clientes.

Este tópico faz parte de um documento maior. Para ler o documento completo, transfira [mundo classe do Azure Resource Manager modelos considerações e práticas comprovada](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

Modelos de combinam as vantagens do Gestor de recursos subjacente do Azure com o adaptability e o facilitar a leitura de JavaScript Object Notation (JSON). Utilizar modelos, pode:

* Implemente topologias e as respetivas cargas de trabalho de forma consistente.
* Gerir todos os recursos de uma aplicação em conjunto com grupos de recursos.
* Aplica o controlo de acesso baseado em funções (RBAC) para conceder acesso adequado aos utilizadores, grupos e serviços.
* Utilizar a marcação de associações para simplificar as tarefas, tais como rollups de faturação.

Este artigo fornece detalhes sobre os cenários de consumo, arquitetura e padrões de implementação identificados durante os nossos sessões de design e implementações de modelo do mundo real com clientes de equipa de Consultadora dos clientes do Azure (AzureCAT). Longe académicas, estas abordagens são comprovadas práticas informadas pelo desenvolvimento de modelos para 12 das tecnologias principais OSS baseado em Linux, incluindo: Apache Kafka, o Apache Spark, Cloudera, Couchbase, Hortonworks HDP, Enterprise DataStax com tecnologia do Apache Cassandra, Elasticsearch, Jenkins, MongoDB, PostgreSQL, Redis e da Nagios. 

Este artigo partilha estas comprovada práticas para o ajudar a architect modelos do mundo classe Azure Resource Manager.  

No nosso funcionam com os clientes, ter identificámos várias experiências de consumo de modelo do Resource Manager em empresas, s integradores de sistema (TAMA) e CSVs. As secções seguintes fornecem uma descrição geral de alto nível de padrões e cenários comuns para tipos de cliente diferentes.

## <a name="enterprises-and-system-integrators"></a>As empresas e integradores de sistema
Dentro de grandes organizações, normalmente, vemos duas consumidores de modelos do Resource Manager: as equipas de desenvolvimento de software interno e empresariais IT. Encontrámos que os cenários para o mapa do SIs para os cenários para empresas, pelo que as mesmas considerações são aplicáveis.

### <a name="internal-software-development-teams"></a>Equipas de desenvolvimento de software interno
Se a sua equipa desenvolvidas pela organização software para suportar a sua empresa, os modelos fornecem uma forma fácil de implementar rapidamente tecnologias para utilização em soluções de negócio específicas. Também pode utilizar modelos para rapidamente criar ambientes de formação que permitem a membros do agrupamento para obterem as competências necessárias.

Pode utilizar modelos como-é, expandir ou compor-los para acomodar as suas necessidades. Utilizar a marcação nos modelos, pode fornecer um resumo de faturação com várias vistas, tais como a equipa, projeto, indivíduo e education.

As empresas, muitas vezes, pretendem as equipas de desenvolvimento de software para criar um modelo para uma implementação consistente de uma solução. O modelo facilita restrições para que determinadas itens dentro desse ambiente permanecerem fixos e não podem ser substituídas. Por exemplo, um banco pode necessitar de um modelo para incluir o RBAC para que um programador não pode rever uma solução de banca para enviar dados para uma conta de armazenamento pessoal.

### <a name="corporate-it"></a>Empresarial IT
As organizações de TI empresariais, normalmente, utilizam modelos para entrega de nuvem capacidades de capacidade e alojado na nuvem.

#### <a name="cloud-capacity"></a>Capacidade de nuvem
É uma forma comum de grupos de TI empresarias fornecer capacidade de nuvem para as equipas com "t-shirt tamanhos", que são padrão oferta tamanhos como pequenas, médias e grandes. As ofertas de t-shirt dimensionadas podem misturar tipos de recursos diferentes e quantidades ao fornecer um nível de padronização torna possível a utilizam modelos. Os modelos fornecem a capacidade de forma consistente, que impõe as políticas de empresa e utiliza a marcação para fornecer o estorno para as organizações a consumir.

Por exemplo, terá de fornecer a ambientes de produção, programação ou teste no qual as equipas de desenvolvimento de software podem implementar as soluções. O ambiente tem uma topologia de rede predefinido e os elementos que não é possível alterar as equipas de desenvolvimento de software, tais como regras que rege acesso para a inspeção de pacotes da internet e público. Também poderá ter funções específicos da organização para estes ambientes com direitos de acesso diferentes para o ambiente.

#### <a name="cloud-hosted-capabilities"></a>Capacidades de nuvem alojada
Pode utilizar modelos para suportar funcionalidades alojado na nuvem, incluindo pacotes de individual software ou compostas ofertas que são oferecidas para linhas internas de negócio. Um exemplo de uma oferta composto seria análise-como-um-serviço — análise, visualização e outras tecnologias — entregar uma configuração otimizada, ligado sobre uma topologia de rede predefinidas.

Capacidades de nuvem alojada são afetadas pelas considerações de segurança e a função estabelecidas pela oferta no qual estão incorporadas a capacidade de nuvem. Estas capacidades são disponibilizadas tal como estão ou como um serviço gerido. Para as funções de última, restrita de acesso são necessárias para ativar o acesso para o ambiente para efeitos de gestão.

## <a name="cloud-service-vendors"></a>Fornecedores de serviço de nuvem
Após falar com vários CSVs, identificámos várias abordagens que pode tomar para implementar serviços para os clientes e os requisitos associados.

### <a name="csv-hosted-offering"></a>Oferta alojados de CSV
Se alojar a sua oferta na sua própria subscrição do Azure, duas abordagens de alojamento são comuns: implementar uma implementação diferente para cada cliente ou para implementar unidades de escala que sustentam uma infraestrutura partilhada utilizada para todos os clientes.

* **Implementações distintas para cada cliente.** Implementações DISTINCT por cliente requerem fixas topologias de diferentes configurações conhecidas. Estas implementações podem ter tamanhos de outra máquina virtual (VM), variando números de nós e diferentes quantidades de armazenamento associadas. Marcação de implementações é utilizado para faturação de rollup de cada cliente. RBAC pode ser ativada para permitir que os clientes acedam aos aspetos do respetivo ambiente de nuvem.
* **Unidades de escala em ambientes de multi-inquilinos partilhados.** Um modelo pode representar uma unidade de escala para ambientes de multi-inquilinos. Neste caso, a mesma infraestrutura é utilizada para suportar todos os clientes. As implementações de representam um grupo de recursos que fornecer um nível de capacidade para a oferta alojada, como o número de utilizadores e o número de transações. Estas unidades de escala são aumentadas ou diminuídas como requer a pedido.

### <a name="csv-offering-injected-into-customer-subscription"></a>Oferta de CSV injetada a subscrição do cliente
Poderá pretender implementar o software em subscrições pertencentes a clientes de fim. Pode utilizar modelos de implementar implementações distintas para a conta do Azure de um cliente.

Estas implementações utilizam o RBAC para que possa atualizar e gerir a implementação numa conta do cliente.

### <a name="azure-marketplace"></a>Azure Marketplace
Anunciar e propor suas ofertas através de um mercado, tais como o Azure Marketplace, pode desenvolver modelos para fornecer tipos de implementações que execute numa conta do Azure de um cliente diferentes. Estas implementações distintas podem ser descritas normalmente como um tamanho de t-shirt (pequena, média, grande), o tipo de produto/público-alvo (Comunidade programador, a empresa) ou o tipo de funcionalidade (básica, de elevada disponibilidade).  Em alguns casos, estes tipos permitem-lhe especificar determinados atributos da implementação, tais como o tipo VM ou o número de discos.

## <a name="oss-projects"></a>Projetos de OSS
Dentro de projetos de código aberto, modelos do Resource Manager permitem uma Comunidade implementar uma solução rapidamente utilizar comprovadas práticas. Pode armazenar modelos no repositório do GitHub, de modo a Comunidade pode revê-los ao longo do tempo. Os utilizadores implementam estes modelos nas respetivas subscrições do Azure.

As seguintes secções identificam as coisas que precisa de considerar antes de conceber a sua solução.

## <a name="identifying-what-is-outside-and-inside-a-vm"></a>Identificar Novidades fora e dentro de uma VM
Como estruturar o seu modelo, é útil ver os requisitos em termos de Novidades fora e em máquinas virtuais (VMs):

* Exterior significa que as VMs e outros recursos da sua implementação, tais como a topologia de rede, etiquetando, refere-se aos certificados/secrets e controlo de acesso baseado em funções. Todos estes recursos fazem parte do seu modelo.
* Interior significa que o software instalado e o estado pretendido geral configuração. Outros mecanismos, tais como extensões VM ou scripts, são utilizados em todo ou em parte. Estes mecanismos podem ser identificados e executados pelo modelo, mas não estão no mesmo.

Exemplos comuns de atividades que "dentro da caixa" inclui-  

* Instalar ou remover funções e funcionalidades
* Instalar e configurar o software ao nível do cluster ou nó
* Implementar Web sites num servidor web
* Implementar os esquemas de base de dados
* Gerir o registo ou outros tipos de definições de configuração
* Gerir ficheiros e diretórios
* Iniciar, parar e gerir serviços e processos
* Gerir contas de utilizador e grupos locais
* Instalar e gerir pacotes (. msi, .exe, yum, etc.)
* Gerir as variáveis de ambiente
* Executar scripts nativos (o Windows PowerShell, bash, etc.)

### <a name="desired-state-configuration-dsc"></a>Configuração de estado pretendido (DSC)
Pensar sobre o estado interno das suas VMs para além de implementação, deve certificar-se de que esta implementação não "que se desviam" da configuração que tenha definido e marcada para o controlo de origem. Esta abordagem garante os programadores ou pessoal de operações não efetuar alterações de ad-hoc ao ambiente que não são vetted, testada ou são registadas no controlo de origem. Este controlo é importante, porque as alterações manuais não estão no controlo de origem. Estes também não fazem parte da implementação padrão e terá impacto futuras implementações automatizadas do software.

Para além dos seus empregados internos, a configuração de estado pretendido também é importante numa perspetiva de segurança. Hackers regularmente estão a tentar comprometer e explorar os sistemas de software. Quando tiver êxito, é comum para instalar os ficheiros e caso contrário, altere o estado de um sistema comprometido. Utilizar configuração de estado pretendido, pode identificar as diferenças entre o estado pretendido e real e restaurar uma configuração conhecida.

Existem extensões de recursos para os mecanismos de mais populares para DSC - PowerShell DSC, Chef e Puppet. Cada uma destas extensões pode implementar o estado inicial da sua VM e também ser utilizada para certificar-se de que o estado pretendido é mantido.

## <a name="common-template-scopes"></a>Âmbitos de modelo comum
Na nossa experiência, iremos viu três âmbitos de modelos de solução chave surgir. Estas três âmbitos – capacidade, a capacidade e ponto-a-ponto solução – são descritos nas secções seguintes.

### <a name="capacity-scope"></a>Âmbito de capacidade
Um âmbito de capacidade fornece um conjunto de recursos numa topologia padrão que está pré-configurada para estar em conformidade com as políticas e regulamentos. O exemplo mais comuns está a implementar num ambiente de desenvolvimento padrão num cenário de TI empresarial ou TAMA.

### <a name="capability-scope"></a>Âmbito de capacidade
Um âmbito de capacidade concentra-se sobre como implementar e configurar uma topologia para uma determinada tecnologia. Cenários comuns, incluindo as tecnologias, como o SQL Server, Cassandra, Hadoop.

### <a name="end-to-end-solution-scope"></a>Âmbito de solução ponto a ponto
Um âmbito de solução ponto a ponto é direcionado para além de uma única capacidade e, em vez disso, concentra-se em fornecer uma solução ponto a ponto composta por várias capacidades.  

Um âmbito de modelo do âmbito de solução manifestos si próprio como um conjunto de um ou mais modelos de âmbito de capacidade com recursos específicos, lógicas e estado pretendido. Um exemplo de um modelo de âmbito de solução é um modelo de solução do pipeline de dados de ponto a ponto. O modelo pode misturar estado e a topologia de solução específicos com vários modelos de âmbito de capacidade de solução, como Kafka, Storm e Hadoop.

## <a name="choosing-free-form-vs-known-configurations"></a>Escolher a forma de libertar vs configurações conhecidas
Se pensa inicialmente um modelo deve dar consumidores a flexibilidade utmost, mas a conta muitos aspetos afetam a escolha de se pretende utilizar configurações de forma livre vs configurações conhecidas. Esta secção identifica os requisitos de cliente de chave e considerações técnicas que em forma a abordagem partilhada neste documento.

### <a name="free-form-configurations"></a>Configurações de forma livre
Na superfície, configurações de forma livre de som ideais. Estes permitem-lhe selecionar um tipo VM e forneça um número aleatório de nós e discos para os nós ligados — e fazê-lo como parâmetros a um modelo. No entanto, esta abordagem não é ideal para alguns cenários.

No [tamanhos das virtual machines](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), são identificados os diferentes tipos de VM e tamanhos disponíveis e cada um do número de durável discos (2, 4, 8, 16 ou 32) que podem ser anexados. Cada disco ligado fornece 500 IOPS e múltiplos destes discos podem ser agrupados para um multiplicador esse número de IOPS. Por exemplo, 16 discos podem ser agrupados para fornecem 8.000 IOPS. Agrupamento é feito com a configuração no sistema operativo, utilizando os espaços de armazenamento do Microsoft Windows ou uma matriz redundante de discos económicos (RAID) no Linux.

Uma configuração de forma gratuita permite a seleção de várias instâncias de VM, VM vários tipos e tamanhos para essas instâncias, vários discos para o tipo VM e um ou mais scripts para configurar o conteúdo VM.

É comum que uma implementação pode ter vários tipos de nós, por exemplo, o mestre de nós e de dados, pelo que esta flexibilidade, muitas vezes, é fornecida para cada tipo de nó.

Como começar a implementar clusters de qualquer significância, começar a trabalhar com estes cenários complexos. Se foram a implementar um cluster do Hadoop, por exemplo, com 8 nós principais e nós de 200 dados e agrupados 4 discos ligados em cada nó principal e agrupados 16 discos ligados por nó de dados, terá de 208 VMs e 3,232 discos para gerir.

Uma conta do storage será limitar pedidos acima que seu identificado 20.000 transações por segundo limite, pelo que deve observar a criação de partições de conta de armazenamento e utilizar cálculos para determinar o número adequado de contas do storage para suportar esta topologia. Cálculos dinâmicos tendo em conta o sem-número de combinações suportadas pela abordagem de forma gratuita, são necessárias para determinar a criação de partições adequado. A linguagem de modelo do Azure Resource Manager fornecem atualmente matemática funções, por isso, tem de efetuar estes cálculos no código, gerar um modelo exclusivo e hard-coded com os dados adequados.

Em TI empresariais e cenários de TAMA, alguém tem de manter os modelos e suportam as topologias implementadas para um ou mais organizações. Esta sobrecarga adicional — configurações diferentes e modelos para cada cliente — é longe desejados.

Pode utilizar estes modelos para implementar ambientes na subscrição do Azure do seu cliente, mas as equipas de TI de empresa e CSVs normalmente implementação-las para as seus próprios subscrições, utilizando uma função de encargos ao faturar os seus clientes. Nestes cenários, o objetivo é implementar a capacidade para vários clientes através de um conjunto das subscrições e manter implementações densely populadas para as subscrições para minimizar sprawl subscrição — ou seja, mais subscrições para gerir. Com tamanhos de implementação verdadeiramente dinâmico, este tipo de densidade de atingirem a total requer um planeamento cuidadoso e desenvolvimento adicional para trabalho andaime em nome da organização.

Além disso, não é possível criar subscrições através de uma chamada à API mas tem de efetuar manualmente, por isso, através do portal. À medida que aumenta o número de subscrições, qualquer sprawl subscrição resultante requer intervenção do homem — não é possível automatizar. Com a variabilidade, tanto que em tamanhos de implementações, teria de pré-Aprovisione um número de subscrições manualmente para garantir que as subscrições estão disponíveis.

Tendo em consideração estes fatores, uma configuração de formulário de libertar verdadeiramente é menos apelativos para resolver a, primeiro blush.

### <a name="known-configurations--the-t-shirt-sizing-approach"></a>Configurações de conhecido — a abordagem de dimensionamento t-shirt
Em vez de oferecer um modelo que proporciona flexibilidade total e variações countless, na nossa experiência um padrão comum consiste em fornecer a capacidade de selecionar configurações conhecidas — em vigor, t-shirt padrão tamanhos como sandbox, pequena, média e grande. Outros exemplos de tamanhos de t-shirt são ofertas de produtos, tais como a Comunidade edition ou enterprise edition.  Noutros casos, poderá ser específico da carga de trabalho configurações uma tecnologia de – como reduzir o mapa ou sem SQL Server.

TI empresariais de muitas organizações, os fornecedores de OSS e SIs disponibilize as suas ofertas de hoje desta forma no local, ambientes virtualizados (empresas) ou como as de ofertas software-como-um-serviço (SaaS) (CSVs e OSVs).

Esta abordagem fornece uma boas, conhecidas configurações de diferentes tamanhos que estão pré-configuradas para clientes. Sem configurações conhecidas, os clientes de fim tem de determinar cluster Dimensionar por si próprios, fator restrições de recursos de plataforma e fazer bibliotecas para identificar a criação de partições resultante de contas de armazenamento e outros recursos (devido a restrições de tamanho e de recursos de cluster). Conhecido configurações permitem aos clientes a selecionar facilmente o tamanho de t-shirt adequado — ou seja, uma determinada implementação. Para além de fazer uma melhor experiência do cliente, um pequeno número de configurações conhecidas é mais fácil suportar e pode ajudar a fornecer um nível mais elevado de densidade.

Uma abordagem de configuração conhecido concentra-se em tamanhos de t-shirt pode também ter variando número de nós dentro de um tamanho. Por exemplo, pode ser um tamanho de pequeno t-shirt entre os nós de 3 e 10.  O tamanho de t-shirt teria de ser concebido para acomodar até 10 nós e fornecem o consumidor a capacidade de efetuar seleções de forma livre até ao tamanho máximo identificado.  

Um tamanho de t-shirt com base no tipo de carga de trabalho, pode ser mais livres formulário natureza em termos do número de nós que pode ser implementado, mas terá de tamanho de nó distintos da carga de trabalho e a configuração do software no nó.

Tamanhos de T-shirt com base nas ofertas de produtos, tais como a Comunidade ou Enterprise, pode ter tipos de recursos diferente e o número máximo de nós que pode ser implementado, normalmente associadas a considerações de licenciamento ou disponibilidade de funcionalidades entre as ofertas de diferentes.

Também pode acomodar a clientes com exclusivos variantes através de modelos baseados em JSON. Ao lidar com valores atípicos, pode incorporar o planeamento e considerações para o desenvolvimento, suporte e custos adequado.

Com base em requisitos identificados no início deste documento e de cenários de consumo de modelo de cliente, identificámos um padrão para decomposição de modelo.

## <a name="capacity-and-capability-scoped-solution-templates"></a>Capacidade e modelos de solução de âmbito de capacidade
Decomposição fornece uma abordagem modular para desenvolvimento de modelos que suporta reutilizar, extensibilidade, teste e as ferramentas. Esta secção fornece detalhes sobre como uma abordagem de decomposição pode ser aplicada aos modelos com um âmbito de capacidade ou capacidade.

Esta abordagem, um modelo de principal recebe os valores dos parâmetros de um consumidor de modelo e hiperligações para vários tipos de modelos e scripts downstream conforme mostrado abaixo. Os parâmetros, variáveis estáticas e variáveis geradas são utilizadas para fornecer os valores que entra e sai de modelos ligados.

![Parâmetros do modelo](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**Os parâmetros são transmitidos para um modelo de principal, em seguida, para modelos ligados**

As secções seguintes focar-se nos tipos de modelos e scripts que um único modelo é decomposed em. As secções apresentam abordagens para passar informações de estado entre os modelos. Cada modelo e os tipos de script na imagem são descritos juntamente com exemplos. Para obter um exemplo contextual, consulte "colocando-o em conjunto: uma implementação de exemplo" mais adiante neste documento.

### <a name="template-metadata"></a>Metadados do modelo
Os metadados do modelo (o ficheiro metadata.json) contém pares chave/valor que descrevem um modelo no JSON, que pode ser lidos por humans e sistemas de software.

![Metadados do modelo](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**Os metadados do modelo é descrito no ficheiro metadata.json**

Agentes de software podem obter o ficheiro metadata.json e publicar as informações e uma hiperligação para o modelo num diretório ou página web. Elementos incluem *itemDisplayName*, *Descrição*, *resumo*, *githubUsername*, e *dateUpdated*.

Um ficheiro de exemplo é mostrado abaixo na íntegra.

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### <a name="main-template"></a>Modelo de principal
O modelo de principal recebe parâmetros de um utilizador, utiliza essa informação para preencher as variáveis de objeto complexo e executa os modelos ligados.

![Modelo de principal](./media/best-practices-resource-manager-design-templates/main-template.png)

**O modelo de principal recebe parâmetros de um utilizador**

Um parâmetro que é fornecido é um tipo de configuração conhecido também conhecido como o parâmetro de tamanho t-shirt devido ao respetivos padronizadas valores, tais como pequena, média, ou grandes. Na prática, pode utilizar este parâmetro de várias formas. Para detalhes, consulte "Conhecido configuração recursos modelo" mais tarde neste documento.

Alguns dos recursos são implementados, independentemente da configuração conhecida especificada por um parâmetro de utilizador. Estes recursos são aprovisionados através de um modelo de recurso partilhado único e são partilhados por outros modelos, para que o modelo de recurso partilhado é executar primeiro.

Alguns dos recursos são implementados, opcionalmente, independentemente da configuração especificada conhecida.

### <a name="shared-resources-template"></a>Modelo de recursos partilhados
Este modelo fornece recursos que são comuns a todas as configurações conhecidas. Contém a rede virtual, conjuntos de disponibilidade e outros recursos que são necessários independentemente do modelo de configuração conhecido que é implementado.

![Recursos de modelo](./media/best-practices-resource-manager-design-templates/template-resources.png)

**Modelo de recursos partilhados**

Os nomes de recursos, tais como o nome de rede virtual, baseiam-se no modelo principal. Pode especificá-los como uma variável de que o modelo ou recebê-las como um parâmetro do utilizador, conforme exigido pela sua organização.

### <a name="optional-resources-template"></a>Modelo de recursos opcionais
O modelo de opcional de recursos contém recursos que são implementados através de programação com base no valor de um parâmetro ou variável.

![Recursos opcionais](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**Modelo de recursos opcionais**

Por exemplo, pode utilizar um modelo de recursos opcionais para configurar um jumpbox que permite acesso indirecta a um ambiente implementado na Internet pública. Pretende utilizar um parâmetro ou variável para identificar se o jumpbox deve estar ativada e o *concat* função para criar o nome de destino para o modelo, tais como *jumpbox_enabled.json*. Ligação de modelo teria de utilizar a variável resultante para instalar o jumpbox.

Pode associar o modelo de recursos opcionais de vários locais:

* Quando aplicável a todas as implementações, crie uma ligação condicionada no parâmetro do modelo de recursos partilhados.
* Quando aplicável para selecionar configurações conhecidas — por exemplo, instalar apenas em grandes implementações — criar uma ligação condicionada por parâmetro ou variável orientada por do modelo de configuração conhecido.

Se um determinado recurso é opcional pode não ser condicionada pelo consumidor modelo, mas pelo fornecedor de modelo. Por exemplo, poderá ter de satisfazer um requisito de produto específica ou um suplemento de produto (comum para CSVs) ou para impor políticas (comuns para SIs e TI empresariais grupos). Nestes casos, pode utilizar uma variável para identificar se o recurso deve ser implementado.

### <a name="known-configuration-resources-template"></a>Modelo de recursos de configuração conhecido
No modelo principal, um parâmetro pode ser disponibilizado para permitir que os consumidores de modelo especificar uma configuração desejada conhecida para implementar. Muitas vezes, esta configuração conhecida utiliza uma abordagem de tamanho t-shirt com um conjunto de tamanhos de configuração fixo como pequenas, médias sandbox, e grande.

![Recursos de configuração conhecido](./media/best-practices-resource-manager-design-templates/known-config.png)

**Modelo de recursos de configuração conhecido**

A abordagem de tamanho t-shirt costuma é utilizada, mas os parâmetros podem representar qualquer conjunto de configurações conhecidas. Por exemplo, pode especificar um conjunto de ambientes de uma aplicação empresarial, como o desenvolvimento, teste e produto. Ou pode utilizar para um serviço em nuvem para representar unidades de escala diferente, as versões de produto ou configurações de produto, como a Comunidade, programador ou Enterprise.

Tal como acontece com o modelo de recursos partilhados, as variáveis são transmitidas para o modelo de configurações conhecidas partir:

* Um utilizador final — ou seja, os parâmetros enviados para o modelo de principal.
* Uma organização — ou seja, as variáveis no modelo principal que representam os requisitos de internos ou políticas.

### <a name="member-resources-template"></a>Modelo de recursos do membro
Dentro de uma configuração conhecida, um ou mais tipos de nó de membro, muitas vezes, são incluídos. Por exemplo, com o Hadoop tiver nós principais e nós de dados. Se estiver a instalar o MongoDB, terá de nós de dados e um arbiter. Se estiver a implementar DataStax, terá de nós de dados e uma VM com OpsCenter instalado.

![Recursos de membros](./media/best-practices-resource-manager-design-templates/member-resources.png)

**Modelo de recursos do membro**

Cada tipo de nós pode ter diferentes tamanhos de VMs, número de discos ligados, scripts, para instalar e configurar os nós, configurações de porta para as VMs, número de instâncias e outros detalhes. Para cada tipo de nó obtém o seu próprio membro modelo de recursos, que contém os detalhes para implementar e configurar uma infraestrutura, bem como executar scripts para implementar e configurar o software dentro da VM.

Para VMs, normalmente dois tipos de scripts são scripts utilizados, amplamente reutilizáveis e personalizados.

### <a name="widely-reusable-scripts"></a>Scripts reutilizáveis amplamente
Scripts reutilizáveis amplamente podem ser utilizadas em vários tipos de modelos. Um dos exemplos destes scripts reutilizáveis amplamente melhor configura o RAID em Linux para o agrupamento de discos e obter um maior número de IOPS. Independentemente do software que está a ser instalado na VM, este script fornece reutilização das práticas comprovadas para cenários comuns.

![Scripts reutilizáveis](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**Modelos de recursos do membro podem chamar scripts reutilizáveis amplamente**

### <a name="custom-scripts"></a>Scripts personalizados
Modelos normalmente chamada uma ou mais scripts que instalar e configurar o software dentro das VMs. Um padrão comum é utilizado com as topologias de grandes dimensões em várias instâncias de um ou mais tipos de membro estão implementadas. Um script de instalação é iniciado para cada VM que pode ser executada em paralelo, seguido de um script de configuração que é chamado depois de todas as VMs (ou todas as VMs de um tipo de membro especificado) são implementadas.

![Scripts personalizados](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**Modelos de recursos do membro podem chamar scripts para um fim específico, como a configuração de VM**

## <a name="capability-scoped-solution-template-example---redis"></a>Exemplo de modelo do âmbito de capacidade de solução - Redis
Para mostrar como uma implementação poderão funcionar, vamos ver um exemplo de prático de criação de um modelo que facilita a implementação e configuração de Redis no tamanhos de t-shirt padrão.  

Para a implementação, existem um conjunto de recursos partilhados (rede virtual, conta de armazenamento, conjuntos de disponibilidade) e um recurso opcional (jumpbox). Existem várias configurações conhecidas representadas como tamanhos t-shirt (pequena, média, grande) mas o tipo de cada um com um único nó. Também existem dois scripts de finalidade específica (configuração, instalação).

### <a name="creating-the-template-files"></a>Criar os ficheiros de modelo
Poderá criar um modelo de Main azuredeploy. JSON.

Criar modelo de recursos partilhados com o nome resources.json partilhado

Criar um modelo do Resource opcional para ativar a implementação de um jumpbox, com o nome jumpbox_enabled.json

Redis utiliza apenas um tipo de nó único, para criar um modelo de recursos de membro único com o nome de nó resources.json.

Com Redis, que pretende instalar cada nó individual e, em seguida, configurar o cluster.  Tem scripts para acomodar a instalação e configuração, install.sh de cluster de redis e setup.sh de cluster de redis.

### <a name="linking-the-templates"></a>Os modelos de ligação
Utilizando o modelo de ligação, as ligações de modelo principal enviados para o modelo de recursos partilhados, que estabelece a rede virtual.

Lógica é adicionada no modelo principal para permitir que os consumidores do modelo especificar se um jumpbox deve ser implementado. Um *ativada* valor para o *EnableJumpbox* parâmetro indica que o cliente pretende implementar um jumpbox. Quando este valor é fornecido, o modelo Concatena *_enabled* como um sufixo para um nome de modelo base para a capacidade de jumpbox.

O principal modelo se aplica a *grande* parâmetro de valor como um sufixo para um nome de modelo base para tamanhos de t-shirt e, em seguida, utiliza esse valor de uma ligação de modelo enviados para *technology_on_os_large.json*.

A topologia seria assemelhar-se nesta ilustração.

![Modelo de redis](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Estrutura do modelo para um modelo de Redis**

### <a name="configuring-state"></a>Configurar o Estado
Para os nós do cluster, existem dois passos para configurar o estado, ambos representadas pelos Scripts específicos de objetivo.  instala o Redis "install.sh de cluster de redis" e "setup.sh de cluster de redis" configura o cluster.

### <a name="supporting-different-size-deployments"></a>Tamanho diferentes implementações de suporte
Dentro as variáveis, o modelo de tamanho t-shirt Especifica o número de nós de cada tipo de implementação para o tamanho especificado (*grande*). Em seguida, implementa esse número de instâncias VM através de ciclos de recursos, fornecer nomes exclusivos a recursos, acrescentando um nome de nó com um número de sequência numérico do *copyindex ()*. Faz estes passos para acesso frequente e transfira zona VMs, tal como definido no modelo de nome t-shirt

## <a name="decomposition-and-end-to-end-solution-scoped-templates"></a>Modelos de solução âmbito decomposição e ponto a ponto
Um modelo de solução com um âmbito de solução ponto-a-ponto concentra-se em fornecer uma solução ponto-a-ponto.  Esta abordagem é normalmente uma combinação de vários modelos com recursos adicionais, lógicas e estado de capacidade de âmbito.

Conforme realçado na imagem abaixo, o mesmo modelo utilizado para modelos de capacidade de um âmbito é expandido para modelos com um âmbito de solução ponto-a-ponto.

Um modelo de recursos partilhados e modelos de recursos opcional servem a mesma função que aparece a capacidade e capacidade de âmbito abordagens de modelo, mas estão no âmbito da solução ponto a ponto.

Como âmbito de solução ponto a ponto modelos também podem normalmente têm t-shirt, o modelo de recursos de configuração conhecido reflete o que é necessário para uma determinada configuração conhecida da solução.

Um âmbito as ligações de modelo de recursos de configuração conhecido para a capacidade de um ou mais modelos de solução que são relevantes para a solução ponto a ponto, bem como os modelos de recursos do membro que são necessárias para a solução ponto a ponto.

Conforme o tamanho de t-shirt da solução pode ser diferente do modelo no âmbito do capacidade individuais, as variáveis do modelo de recursos de configuração conhecido de são utilizadas para fornecer os valores adequados para modelos de solução de capacidade a jusante um âmbito implementar o tamanho de t-shirt adequado.

![Ponto a ponto](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**O modelo utilizado para a capacidade ou capacidade de modelos de solução âmbito podem ser expandidos prontamente para âmbitos de modelo de solução ponto a ponto**

## <a name="preparing-templates-for-the-marketplace"></a>Preparar os modelos do Marketplace
A abordagem anterior prontamente acomoda cenários em que as empresas, SIs e CSVs pretendem implementar modelos próprios ou ativar os seus clientes implementar por si próprios.

Outro pretendido cenário está a implementar um modelo através do marketplace.  Esta abordagem decomposição funciona para o marketplace, com algumas pequenas alterações.

Como mencionado anteriormente, os modelos podem ser utilizados para oferecer tipos de implementação diferentes para venda no marketplace. Tipos de implementação distintos poderá tamanhos t-shirt (pequena, média, grande), o tipo de produto/público-alvo (Comunidade programador, a empresa) ou o tipo de funcionalidade (básica, de elevada disponibilidade).

Como é mostrado abaixo, a solução ponto a ponto existente ou modelos de capacidade de um âmbito podem ser prontamente utilizados para listar as configurações diferentes conhecidas no marketplace.

Os parâmetros no modelo principal primeiro são modificados para remover o parâmetro de entrada com o nome tshirtSize.

Ao mapeiam os tipos de implementação diferentes para o modelo de recursos de configuração conhecido, também têm de recursos comuns e configuração encontrado no modelo de recursos partilhados e potencialmente nas opcional de recursos de modelos.

Se pretender publicar o modelo para o mercado, estabelecer cópias distintas seu Main do modelo de que substitui o parâmetro de entrada anteriormente disponível de tshirtSize a uma variável incorporada do modelo de.

![Marketplace](./media/best-practices-resource-manager-design-templates/marketplace.png)

**Adaptação uma solução de âmbito de modelo para o marketplace**

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o estado de partilha, dentro e fora de modelos, consulte [partilha Estado em modelos do Azure Resource Manager](best-practices-resource-manager-state.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

