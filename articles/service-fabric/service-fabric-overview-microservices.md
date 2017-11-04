---
title: "Introdução ao micro-serviços no Azure | Microsoft Docs"
description: "Uma descrição geral do motivo pelo qual a criação de aplicações em nuvem com uma abordagem de micro-serviços é importante para o desenvolvimento de aplicações modernas e como o Service Fabric do Azure fornece uma plataforma para conseguir isto."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: f69f594d058ba061cec116f87435c96280e19f93
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Por que motivo um micro-serviços abordagem de criação de aplicações?
Como os programadores de software, não há nada de novo na forma como acreditamos sobre factoring uma aplicação em partes de componente. É o paradigma central de orientação de objeto, abstrações de software e componentization. Hoje em dia, este factorization tende a ter a forma de classes e interfaces entre camadas de tecnologia e bibliotecas partilhadas. Normalmente, é necessária uma abordagem em camadas com um arquivo de back-end, lógica de negócio de camada média e uma interface de front-end de utilizador (IU). O que *tem* alterados ao longo dos últimos anos alguns é que podemos, como os programadores, está a criar aplicações que são para a nuvem distribuídas e condicionadas pela empresa.

As alteração necessidades comerciais são:

* Um serviço que é criado e funciona em escala para alcançar os clientes em regiões geográficas novo (por exemplo).
* Entrega mais rápida de funcionalidades e capacidades para conseguir responder a pedidos de cliente de uma forma seja ágil.
* Utilização de recursos melhorada para reduzir os custos.

Estas necessidades de negócio são afetar *como* iremos criar aplicações.

Para obter mais informações sobre a abordagem do Azure para micro-serviços, leia o artigo [micro-serviços: uma rotações de aplicação que utiliza a tecnologia de nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolithic vs abordagem de conceção de microsserviço
Todas as aplicações evoluem ao longo do tempo. Aplicações com êxito evoluem pelo facto de ser úteis para as pessoas. Aplicações sem não evoluem e, eventualmente, foram preteridas. A pergunta torna-se: quanto sabe sobre os requisitos de hoje em dia, e o que irá ser no futuro? Por exemplo, digamos que está a criar uma aplicação de relatórios para um departamento. Tem a certeza de que a aplicação permanece no âmbito da sua empresa e de que os relatórios de curta duração. À sua escolha abordagem é diferente do, diga, criação de um serviço que oferece conteúdo de vídeo para dezenas de milhões de clientes. 

Por vezes, obter algo saída da porta como prova de conceito é o fator despertar, enquanto sabe que a aplicação pode ser foi reestruturada, mais tarde. Não há pouca ponto excessiva de engenharia algo que nunca é utilizado. É o compromisso de engenharia habitual. Por outro lado, quando as empresas abordadas edifício para a nuvem, as expectativas são de crescimento e de utilização. O problema é que o crescimento e escala estão imprevisíveis. Gostaríamos de conseguir criar protótipos rapidamente sabendo também que iremos estiverem num caminho de lidar com êxito futuro. Esta é a abordagem de arranque lean: criar, medidas, saiba e iterar.

Durante a era cliente-servidor, iremos tended focar-se na criação de aplicações em camadas através de tecnologias específicas em cada camada. O termo *monolithic* aplicação tem emerged para estas abordagens. As interfaces tended deve ser entre as camadas e foi utilizado um design mais fortemente conjugado entre componentes em cada camada. Os programadores concebidos e factored classes que foram compiladas em bibliotecas e ligadas em conjunto para alguns executáveis e DLLs. 

Existem vantagens para uma abordagem de monolithic design. Muitas vezes, é mais simples de design e tem chamadas mais rápidas entre os componentes, porque estas chamadas são, muitas vezes, através de comunicação entre processos (IPC). Além disso, todos os testes de um único produto, tende a ser mais pessoas recursos eficiente. O downside é que existe uma sólida coupling entre camadas em camadas, e não pode dimensionar componentes individuais. Se precisar de realizar correções ou atualizações, terá de aguardar por outras pessoas concluir os testes. É mais difícil ser seja ágil.

Micro-serviços endereços estes downsides e mais alinhada com os requisitos de negócio anterior, mas também têm benefícios e liabilities. Os benefícios da Micro-serviços são que cada um deles encapsula normalmente funcionalidades empresariais mais simples, o que pode aumentar ou reduzir verticalmente, teste, implementar e gere de forma independente. Uma vantagem importante de uma abordagem de microsserviço é que as equipas são condicionadas mais por cenários de negócios que através da tecnologia encoraja a abordagem em camadas. Na prática, mais pequenas equipas desenvolver um microsserviço com base no cenário de cliente em utilizam as tecnologias que escolherem. 

Por outras palavras, a organização não tem de normalizar técnico para manter as aplicações de microsserviço. Equipas individuais que os seus próprios serviços podem fazer que é adequado para os mesmos com base nos conhecimentos de equipa ou o que é mais adequado para resolver o problema. Na prática, um conjunto de tecnologias recomendadas, tais como um determinado NoSQL arquivo ou estrutura da aplicação de web, é preferível.

Downside de micro-serviços é apresentada no gerir o aumento do número de entidades separadas e lidar com implementações mais complexas e controlo de versões. Tráfego de rede entre os micro-serviços aumenta, bem como as latências de rede correspondente. Muitos serviços chatty, granulares são uma receitas para um nightmare de desempenho. Sem ferramentas para ajudar a ver estas dependências, é difícil de "ver" todo o sistema. 

As normas tornar a abordagem de microsserviço escolar aceita sobre a comunicar e ao ser tolerante a apenas as coisas que precisa de um serviço, em vez de rigid contratos. É importante definir estes contratos adiantado na estrutura, porque os serviços de atualização independente entre si. Outra descrição coined para estruturar com uma abordagem de micro-serviços é "detalhada serviço arquitetura orientada (SOA)."

***No respetivo mais simples, a abordagem de design micro-serviços é sobre Federação dos serviços, com alterações independentes para cada e normas acordado, para a comunicação desacoplada.***

Como são produzidas mais aplicações na nuvem, as pessoas detetar que este decomposição da aplicação global para os serviços de independentes, concentra-se cenário é uma abordagem de melhor a longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicações
![Desenvolvimento de aplicações de plataforma do Service Fabric][Image1]

1) Uma aplicação monolithic contém funcionalidades específicas do domínio e normalmente é dividida por camadas funcionais, por exemplo, dados, negócio e web.

2) Pode dimensionar a uma aplicação monolithic através da clonagem-lo em vários servidores/máquinas virtuais/contentores.

3) Uma aplicação de microsserviço separa funcionalidade nos serviços de menores separados.

4) As escalas de abordagem micro-serviços terminar através da implementação de cada serviço independentemente, criar instâncias destes serviços em máquinas virtuais/servidores/contentores.

Conceber com um microsserviço abordagem não é um panacea para todos os projetos, mas mais detalhadamente alinhar com os objetivos de negócio descritos anteriormente. Começar com uma abordagem monolithic poderá ser aceitável se souber que terá a oportunidade de rework o código mais tarde para uma estrutura de micro-serviços. Mais frequentemente, pode começa com uma aplicação monolithic e lentamente divida-a em fases, começando pelo funcionais áreas que têm de ser mais dimensionável, ou seja ágil.

Para resumir, a abordagem de microsserviço é para compor a sua aplicação de vários serviços pequenos. Os serviços são executados nos contentores que são implementados através de um cluster de máquinas. Menores equipas desenvolver um serviço que se centra num cenário e independentemente de teste, versão, implementar e dimensionar cada serviço, para que a aplicação completa pode evoluem.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?
Existem definições diferentes das micro-serviços. Se procurar da Internet, encontrará muitos recursos úteis que fornecem as suas próprias viewpoints e definições. No entanto, a maioria das seguintes características da Micro-serviços é amplamente acordada:

* Encapsulam um cenário de negócio ou cliente. O que é o problema que está a resolver?
* Desenvolvido por uma pequena equipa de engenharia.
* Escritas em qualquer linguagem de programação e utilizar qualquer estrutura.
* São compostos de código e (opcionalmente). o estado, que são independentemente com versão do, implementadas e ampliada.
* Interagir com outros micro-serviços através de interfaces bem definidas e protocolos.
* Ter nomes exclusivos (URL) utilizados para resolver a respetiva localização.
* Permanecer consistente e estão disponíveis na presença de falhas.

Pode resumem estas características em:

***Aplicações de Microsserviço são compostas por pequenos, independentemente com versão e dimensionáveis concentra-se ao cliente os serviços que comunicam entre si através de protocolos padrão com interfaces bem definidas.***

Iremos abordadas os primeiros dois pontos na secção anterior e agora vamos expanda no e esclarecer a outras pessoas.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Escritas em qualquer linguagem de programação e utilizar qualquer estrutura
Como programadores, deverá ser livres escolher uma linguagem ou arquitetura que queremos, dependendo do nosso competências ou as necessidades do serviço. Em alguns serviços, poderá valor os benefícios de desempenho do C++ acima de todos os pessoa. Outros serviços, poderá ser mais importante a facilidade de desenvolvimento gerido em c# ou Java. Em alguns casos, poderá ter de utilizar uma biblioteca parceira específica, a tecnologia de armazenamento de dados ou a meio de expor o serviço para clientes.

Depois de escolher uma tecnologia que vêm de gestão operacional ou ciclo de vida e dimensionamento do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e o estado é de forma independente com versão do, implementadas e escalado
No entanto escolha escrever o seu micro-serviços, o código e, opcionalmente, o estado deverá independentemente implementar, atualizar e, de escala. Isto é, na verdade, um dos problemas mais difícil de resolver, porque se trata de à sua escolha de tecnologias. Para compreender o dimensionamento, como por partição (ou partições horizontais) o código e o estado é um desafio. Quando o código e o estado de utilizam tecnologias separadas, que é comum hoje em dia, os scripts de implementação para a sua microsserviço tem de ser capaz de lidar com dimensionamento-los ambas. Este é também sobre agilidade e a flexibilidade, pelo que pode atualizar algumas dos micro-serviços sem ter de atualizar todos eles em simultâneo.

Regressar à monolithic versus microsserviço abordagem para um momento, o diagrama seguinte mostra as diferenças na abordagem para armazenar o estado.

#### <a name="state-storage-between-application-styles"></a>Armazenamento de estado entre os estilos de aplicação
![Armazenamento de Estados de plataforma do Service Fabric][Image2]

***A abordagem monolithic no lado esquerdo tem uma base de dados e de camadas de tecnologias específicas.***

***A abordagem de micro-serviços à direita tem um gráfico de micro-serviços interligados onde o estado é normalmente no âmbito de microsserviço e várias tecnologias são utilizadas.***

Uma abordagem monolithic, normalmente, a aplicação utiliza uma base de dados. A vantagem é que é uma localização única, o que torna mais fácil de implementar. Cada componente, pode ter uma única tabela para armazenar o estado. As equipas necessário separar estritamente Estado, o que é um desafio. Inevitavelmente existem temptations para adicionar uma nova coluna a uma tabela de cliente existente, efetue uma associação entre as tabelas e criar dependências na camada de armazenamento. Depois de esta situação acontece, não é possível aumentar componentes individuais. 

A abordagem de micro-serviços, cada serviço gere e armazena o estado da sua própria. Cada serviço é responsável por código e o estado em conjunto para satisfazer os pedidos de serviço. Um downside é que quando for necessário para criar vistas ou consultas, dos dados da sua aplicação, tem de consultar em estado de diferentes arquivos. Normalmente, isto é resolvido por ter um microsserviço separado que cria uma vista através de uma coleção de micro-serviços. Se precisar de efetuar várias consultas impromptu nos dados, cada microsserviço deve considerar a escrever os dados para um serviço warehousing de dados para análise offline.

Controlo de versões é específico para a versão implementada de um microsserviço, por isso, esse várias versões diferentes implementar e executar lado. Os cenários em que uma versão mais recente de um microsserviço falha durante a atualização e tem de reverter para uma versão anterior de endereços de controlo de versões. O outro cenário para controlo de versões está a efetuar testes A/B-estilo, onde os utilizadores diferentes experiência versões diferentes do serviço. Por exemplo, é comum para atualizar um microsserviço para um conjunto específico de clientes para testar a nova funcionalidade antes de a disponibilizar mais amplamente. Depois de gestão de ciclo de vida dos micro-serviços, agora dá-nos para comunicação entre elas.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros micro-serviços através de interfaces bem definidas e protocolos
Este tópico necessita de pouca atenção aqui, porque um vasto conjunto literature sobre a arquitetura orientada para serviços que foi publicada ao longo dos últimos anos 10 descreve os padrões de comunicação. Geralmente, a comunicação de serviço utiliza uma abordagem REST com protocolos HTTP e TCP e XML ou JSON como o formato de serialização. A partir de uma perspetiva de interface, é sobre adoção a abordagem de conceção da web. Mas nada o deixa de utilizar protocolos binários ou os seus próprios formatos de dados. Estar preparadas para as pessoas tenham uma vez mais difícil utilizando o seu micro-serviços se estes protocolos e formatos não estão disponíveis abertamente.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome exclusivo utilizado para resolver a localização (URL)
Lembre-se como vamos manter a indicar que a abordagem de microsserviço é como web? Como a web, o microsserviço tem de ser endereçável independentemente do local onde se encontra em execução. Se estiver a pensar sobre máquinas e qual está a executar um determinado microsserviço, coisas correrem incorretas rapidamente. 

Da mesma forma que o DNS resolve um determinado URL para um computador específico, o microsserviço tem de ter um nome exclusivo para que seja detetável a sua localização atual. Micro-serviços têm nomes endereçáveis-as independentes da infraestrutura de que estes estão em execução. Isto implica que há uma interação entre a forma como o seu serviço é implementado e como é detetado, porque tem de ser um registo de serviço. Igualmente, quando uma máquina falha, o serviço de registo deve indicar onde o serviço está agora a ser executado. 

Isto permite beneficiar de-nos para o tópico seguinte: resiliência e consistência.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponíveis na presença de falhas
Lidar com falhas inesperadas é um dos problemas hardest para resolver, especialmente num sistema distribuído. Muito do código que iremos escrever como programadores está a processar as exceções e este é também onde os mais tempo é gasto no teste. O problema é mais envolvido que escrever código para processar falhas. O que acontece quando a máquina onde está a executar o microsserviço falhar? Não só é necessário detetar esta falha de microsserviço (um problema no seu próprio disco rígido), mas também terá de algo ao reiniciar o seu microsserviço. 

Um microsserviço tem sejam resilientes a falhas e, muitas vezes, reiniciar noutra máquina por motivos de disponibilidade. Isto também é fornecido para baixo para o estado que foi guardado em nome de microsserviço, onde o microsserviço pode recuperar deste Estado do e se o microsserviço é capaz de reiniciar com êxito. Por outras palavras, tem de haver resiliência na computação (o processo reinicia), bem como a resiliência no Estado ou de dados (sem perda de dados e os dados permanecem consistentes).

Os problemas de resiliência são compounded durante a outros cenários, como quando falhas ocorrem durante uma atualização da aplicação. Microsserviço, trabalhar com o sistema de implementação, não precisa de recuperar. Também tem de decidir se poder continuar para avançar para a versão mais recente ou em vez disso, reverter para uma versão anterior para manter um estado consistente. Perguntas, tais como se suficiente máquinas estão disponíveis para manter mover reencaminhar e como recuperar versões anteriores de microsserviço tem de ser considerados. Isto requer microsserviço para emitir informações de estado de funcionamento para conseguir efetuar estas decisões.

### <a name="reports-health-and-diagnostics"></a>Estado de funcionamento de relatórios e diagnóstico
Pode parecer óbvios e são muitas vezes ignorada, mas um microsserviço deve comunicar o estado de funcionamento e diagnósticos. Caso contrário, não há poucas informações de uma perspetiva de operações. Correlacionar eventos de diagnóstico através de um conjunto de serviços independentes e lidar com a máquina relógio skews para fazer sentido da ordem de eventos é um desafio. Da mesma forma que interagem com um microsserviço através de protocolos acordado e os formatos de dados, emerges existe necessidade de uniformização em como estado de funcionamento e eventos de diagnóstico que basicamente acabar num arquivo de eventos para consultar e visualização de registo. Uma abordagem de micro-serviços é chave que concorda com equipas diferentes no formato de um único registo. Tem de haver uma abordagem consistente para visualizar eventos de diagnóstico da aplicação como um todo.

Estado de funcionamento é diferente do diagnóstico. Estado de funcionamento está prestes o microsserviço relatórios de estado atual para executar as ações adequadas. Um bom exemplo está a trabalhar com mecanismos de atualização e implementação para manter a disponibilidade. Apesar de um serviço pode estar danificado devido a uma falha de processo ou reinicialização do computador, o serviço poderá ainda estar operacional. É a última coisa que precisa tornar esta um efetuando uma atualização. A melhor abordagem para se efetuar uma investigação pela primeira vez ou aguarde algum tempo para microsserviço recuperar. Eventos de estado de funcionamento de um microsserviço ajudam-na tomar decisões informadas e, em vigor, ajudar a criar serviços autorrecuperação.

## <a name="service-fabric-as-a-microservices-platform"></a>Serviço de recursos de infraestrutura como uma plataforma de micro-serviços
Azure Service Fabric emerged de uma transição pela Microsoft, de entrega de produtos de caixa, que foram normalmente monolithic no estilo, a entrega de serviços. A experiência de criação e serviços grandes, como SQL Database do Azure e Azure Cosmos DB, a funcionar em forma de Service Fabric. A plataforma evoluiu e deu lugar ao longo do tempo como serviços mais adotado uma larga maioria-lo. Importante ainda, o Service Fabric tinha a execução não apenas no Azure, mas também das implementações autónomas do Windows Server.

***O objetivo de Service Fabric é resolver os problemas de disco rígidos de criar e executar um serviço e utilizam os recursos de infraestrutura de forma eficiente, para que as equipas podem resolver problemas de empresas utilizando uma abordagem de micro-serviços.***

O Service Fabric fornece três áreas abrangentes para o ajudar a criar aplicações que utilizam uma abordagem de micro-serviços:

* Uma plataforma que fornece serviços do sistema para implementar, atualizar, detetar e reinicie os serviços de falha, detetar serviços, encaminhar mensagens, gerir o estado e monitorizar estado de funcionamento. Estes serviços do sistema em vigor ativar muitas das características da Micro-serviços descritos anteriormente.
* Capacidade para implementar aplicações ou em execução nos contentores ou como processos. Recursos de infraestrutura de serviço é um contentor e orchestrator de processo.
* APIs de programação produtivas, para o ajudar a criar aplicações como micro-serviços: [ASP.NET Core, Reliable Actors e Reliable Services](service-fabric-choose-framework.md). Pode escolher qualquer código para criar a sua microsserviço. Mas estas APIs efetuar a tarefa mais simples e integram com a plataforma de um nível mais aprofundado. Desta forma, por exemplo, pode obter informações de estado de funcionamento e diagnósticos ou, pode tirar partido de elevada disponibilidade incorporada.

***Service Fabric é agnóstico relativamente sobre como criar o seu serviço, e pode utilizar qualquer tecnologia. No entanto, este fornecer APIs de programação incorporadas que torna mais fácil criar micro-serviços.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrar as aplicações existentes para o Service Fabric
Uma abordagem de chave para o Service Fabric está a reutilizar o código existente, que, em seguida, pode ser modernized com micro-serviços novo. Existem cinco fases para modernization de aplicação e pode iniciar e parar em qualquer um das fases. Estes são;

1) Colocar uma aplicação monolithic tradicional  
2) Comparação de precisão e deslocar - utilizar contentores ou executáveis de convidado para alojar código existente no Service Fabric.  
3) Modernization - novo micro-serviços adicionados juntamente com o código de existente.  
4) Inovar - quebrar o monolithic micro-serviços puramente com base na necessidade.  
5) Transformado micro-serviços - a transformação de existente monolithic aplicações ou criar novas aplicações greenfield.

![Migração para micro-serviços][Image3]

É importante realçar novamente que pode **iniciar e parar em qualquer um destas fases**. Não são formos forçados a progresso para a próxima fase. Vamos agora ver exemplos para cada um destas fases.

**Comparação de precisão e deslocar** -grandes quantidades de empresas são lifting e mudarem aplicações monolithic existentes em contentores por duas razões;

- Redução de custo ou devido a consolidação e a remoção de aplicações existentes de hardware ou com a densidade superior. 
- Contrato de implementação consistente entre desenvolvimento e operações.

Custos reductions são compreensíveis e na Microsoft, grande número de aplicações existentes está a ser de simplesmente para guardar milhões de utilizados no compromisso. Implementação consistente é mais difícil avaliar, mas como igualmente importante. Diz que os programadores podem ainda ser livres escolher a tecnologia que conjuntos-las, no entanto, as operações aceitar apenas uma única forma de implementar e gerir estas aplicações. Reduz os quando as operações de ter de lidar com a complexidade de muitas tecnologias diferentes ou forçar os programadores de escolher apenas determinados aqueles. Essencialmente, cada aplicação é de imagens de implementação autónomo.

Muitas organizações parar aqui. Que já têm as vantagens de contentores e o Service Fabric fornece a experiência de gestão completa da implementação, atualizações, controlo de versões, reverte, etc de monitorização de estado de funcionamento.

**Modernization** -consiste na adição de novos serviços em conjunto com o código de existente. Se pretender escrever o novo código, é melhor optar por adotar breves passos para baixo o caminho de micro-serviços. Isto foi possível adicionar um novo ponto final de REST API ou novo lógica de negócio. Desta forma, iniciar num journey de micro-serviços novo edifício e práticas de desenvolvimento e implementação dos mesmos.

**Inovar** -Lembre-se esses original alteração necessidades comerciais no início deste artigo, que estão a impulsionar a abordagem de micro-serviços? Nesta fase que é a decisão, estes estavam a acontecer a minha aplicação atual e, se assim for, necessário iniciar dividir o monolith ou innovating. Um exemplo aqui é quando uma base de dados se tornar um estrangulamento do processamento, uma vez que está a ser utilizado como uma fila de fluxo de trabalho. O número de fluxo de trabalho pedidos aumentar o trabalho tem de ser distribuídas para o dimensionamento. Para essa informação específica da aplicação que não é necessário atualizar mais frequentemente do dimensionamento, ou, dividir este limite num microsserviço e inovar. 

**Transformado micro-serviços** -esta é a onde a aplicação totalmente composto (ou decomposed para) micro-serviços. Para alcançar aqui, efetuou a journey micro-serviços. Pode começar aqui, mas fazer sem um micro-serviços plataforma para o ajudar a é um investimento significativo. 

### <a name="are-microservices-right-for-my-application"></a>São micro-serviços à direita para a minha aplicação?
Talvez. Iremos teve foi que como mais equipas na Microsoft começou a criar para a nuvem por razões de negócio, muitos dos mesmos realizados as vantagens de seguindo uma abordagem de microsserviço semelhante. Bing, por exemplo, tenha sido desenvolver micro-serviços na pesquisa de anos. Para outras equipas, a abordagem de micro-serviços era novo. As equipas encontrado que não existe foram disco rígidos problemas para resolver fora do respetivas áreas de núcleos da força. Esta é a razão pela qual o Service Fabric adquiridos traction como a tecnologia de eleição para criação de serviços.

O objetivo do Service Fabric consiste em reduzir as complexidades de criação de aplicações com uma abordagem de microsserviço, para que não terá de percorrer como redesigns muitas dispendiosos. Comece por algo pequeno, dimensionar quando for necessário, despromover serviços, adicionar novos e evoluir com o cliente utilização é a abordagem. Também Sabemos que existem muitos outros problemas ainda a ser resolvidos para tornar micro-serviços mais acessível para a maior parte dos programadores. Contentores e o modelo de programação de ator são exemplos de passos pequenos do que direção e estamos a certeza de que mais inovações irão surgir para facilitar o processo.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral de terminologia do Service Fabric](service-fabric-technical-overview.md)
* [Micro-serviços: Uma aplicação rotações utiliza a tecnologia de nuvem](https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
