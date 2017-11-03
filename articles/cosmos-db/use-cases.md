---
title: "Casos de utilização comum e cenários para a base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba mais sobre a parte superior cinco casos de utilização do Azure Cosmos DB: utilizador gerado conteúdo, registo de eventos, dados de catálogo, dados de preferências do utilizador e Internet das coisas (IoT)."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: mimig
ms.openlocfilehash: edbed5654a4df8a28b43f03ffd0ac204e0d7f8b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casos de utilização de base de dados do Azure Cosmos comuns
Este artigo fornece uma descrição geral de vários casos de utilização comuns para a base de dados do Azure Cosmos.  As recomendações neste artigo, servem como um ponto de partida, como desenvolver a sua aplicação com a base de dados do Cosmos.   

Depois de ler este artigo, poderá responder às seguintes questões: 

* Quais são os casos de utilização comuns para a base de dados do Azure Cosmos?
* Quais são as vantagens de utilizar a base de dados do Azure Cosmos para aplicações de revenda?
* Quais são as vantagens da utilização de base de dados do Azure Cosmos como um arquivo de dados em sistemas de Internet das coisas (IoT)?
* Quais são as vantagens da utilização de base de dados do Azure Cosmos em aplicações móveis e web?

## <a name="introduction"></a>Introdução
[BD do Azure do Cosmos](../cosmos-db/introduction.md) é serviço de base de dados globalmente distribuída da Microsoft. O serviço foi concebido para permitir aos clientes aprovisionadas (e independentemente) Dimensionar débito e o armazenamento em qualquer número de regiões geográficas. BD do Azure do Cosmos é o primeiro serviço de base de dados globalmente distribuída no mercado hoje para oferecer abrangente [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/cosmos-db/) que abrange o débito, latência, disponibilidade e consistência. 

O projeto de base de dados do Azure Cosmos iniciado em 2011 como "Projeto Florence" para o endereço programador-pontos de tensão que são confrontadas por aplicações de Internet escala grandes dentro da Microsoft. Observar que estes problemas não são exclusivos para aplicações da Microsoft, decidimos disponibilizar Azure Cosmos DB geralmente aos programadores externos no 2015 no formato [Azure DocumentDB](https://azure.microsoft.com/blog/documentdb-moving-to-general-availability/). O serviço é utilizado ubiquitously internamente na Microsoft e é um dos serviços de mais rápido crescente utilizados pelos programadores do Azure externamente. 

BD do Cosmos do Azure é global distribuído, com vários modelo base de dados que é utilizada numa vasta gama de aplicações e casos de utilização. É uma boa opção para qualquer [sem servidor](http://azure.com/serverless) aplicação que necessita de tempos de resposta de ordem de milissegundo baixa e tem de dimensionar rapidamente e global. Suporta vários modelos de dados (chave-valor, documentos, gráficos e columnar) e muitas APIs para dados de acesso, incluindo [MongoDB](mongodb-introduction.md), [SQL do DocumentDB](documentdb-introduction.md), [Gremlin](graph-introduction.md), e [tabelas do Azure](table-introduction.md) nativamente e de forma extensível. 

Seguem-se alguns atributos de base de dados do Azure Cosmos que torna adequada para aplicações de elevado desempenho com global ambition.

* BD do Azure do Cosmos nativamente particiona os dados para elevada disponibilidade e escalabilidade. BD do Azure do Cosmos oferece garantias de 99,99% de disponibilidade, débito, latência baixa e consistência.
* BD do Azure do Cosmos tem um armazenamento de cópias de SSD com tempos de resposta de ordem de milissegundo de latência baixa.
* Suporte Cosmos da BD do Azure para níveis de consistência como prefixo eventual e consistente, a sessão e a consistência vinculada permite total flexibilidade e o rácio de custos para desempenho baixo. Nenhum serviço de base de dados oferece a maior quantidade flexibilidade como base de dados do Azure Cosmos na consistência níveis. 
* BD do Azure do Cosmos tem um modelo de preços do compatível com dados flexível que medidores débito de armazenamento e de forma independente.
* Modelo de débito reservado da BD Cosmos do Azure permite-lhe pensar em termos de número de leituras/escritas em vez da CPU/memória/IOPs o hardware subjacente.
* Permite de design do Azure BD Cosmos que Dimensionar para volumes de pedido em grande escala, por ordem de biliões de pedidos por dia.

Estes atributos são úteis na web, móveis, jogos e IoT aplicações que necessitam tempos de resposta baixa e tem de processar quantidades enormes de leituras e escritas.

## <a name="iot-and-telematics"></a>IoT e telemática
Casos de utilização de IoT normalmente partilham alguns padrões na forma como estes de inserção, processo e armazenam dados.  Em primeiro lugar, estes sistemas têm de inserção bursts de dados de sensores de dispositivo de várias regiões. Em seguida, estes sistemas processam e analisam dados de transmissão em fluxo derivar informações em tempo real. Os dados, em seguida, são arquivados ao armazenamento de frio para análise de lote. O Microsoft Azure oferece incluindo BD do Cosmos do Azure, Event Hubs do Azure, Azure Stream Analytics, Notification Hub do Azure, Azure Machine Learning, Azure HDInsight e PowerBI de casos de utilização de serviços avançados que podem ser aplicados para IoT. 

![Arquitetura de referência do Cosmos DB IoT do Azure](./media/use-cases/iot.png)

Podem ser ingeridos bursts dos dados ao Event Hubs do Azure como oferece ingestão de dados de débito elevado com baixa latência. Dados ingeridos que tem de ser processados para conhecimentos aprofundados em tempo real podem ser funneled ao Azure Stream Analytics para análise em tempo real. Dados podem ser carregados para base de dados do Azure Cosmos para consultas ad hoc. Depois dos dados são carregados na base de dados do Azure Cosmos, os dados estão prontos para ser consultado. Além disso, os novos dados e as alterações aos dados existentes podem ser lidos no feed de alteração. Alteração feed é um persistente, apenas os registos que armazena as alterações nas coleções de BD do Cosmos ordem sequencial de acréscimo. A todos os dados ou apenas as alterações aos dados na base de dados do Azure Cosmos podem ser utilizadas como dados de referência como parte da análise em tempo real. Além disso, dados ainda podem ser foi refinados e processados através da ligação de dados do Azure Cosmos DB para o HDInsight para as tarefas do Pig, Hive ou mapa/reduza.  Avançadas carregar os dados é, em seguida, voltar a BD do Cosmos do Azure para relatórios.   

Para uma solução de IoT de exemplo através do Azure Cosmos DB, EventHubs e Storm, consulte o [exemplos de storm hdinsight repositório no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obter mais informações sobre as ofertas do Azure para IoT, consulte [criar Internet das coisas Your](http://www.microsoft.com/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Revenda e marketing
BD do Azure do Cosmos é amplamente utilizado em plataformas de comércio da Microsoft, com a loja Windows e, em seguida, XBox Live. Também é utilizado na indústria de revenda para armazenar dados de catálogo e para o evento sourcing por ordem de processamento de pipelines.

Cenários de utilização do catálogo de dados envolvem armazenar e consultar um conjunto de atributos para entidades, como as pessoas, locais e produtos. Alguns exemplos de dados do catálogo são contas de utilizador, catálogos de produtos, registos de dispositivos de IoT e fatura dos sistemas de materiais. Atributos para estes dados podem variar e podem alterar ao longo do tempo para se ajustar os requisitos da aplicação.

Considere um exemplo de um catálogo de produtos para um fornecedor de automotive partes. Cada parte pode ter os seus próprios atributos, para além de atributos comuns que partilham todas as partes. Além disso, os atributos de uma parte específica podem alterar o ano seguinte, quando for lançado um novo modelo. BD do Azure do Cosmos suporta esquemas flexíveis e de dados hierárquicos e, por conseguinte, é também adequada para armazenar dados de catálogo do produto.

![Azure arquitetura de referência do catálogo de revenda do Cosmos DB](./media/use-cases/product-catalog.png)

BD do Azure do Cosmos é frequentemente utilizada para eventos sourcing para arquiteturas de evento orientadas por power utilizando o respetivo [alterar feed](change-feed.md) funcionalidade. O feed de alteração fornece a capacidade de forma incremental e fiável leitura atualizações (por exemplo, eventos de ordem) efetuadas para uma base de dados do Azure Cosmos e inserções de micro-serviços a jusante. Esta funcionalidade pode ser aproveitada para fornecer um arquivo persistente eventos como um mediador de mensagens de eventos de alteração de estado e fluxo de trabalho do unidade ordem processamento entre vários micro-serviços (que podem ser implementadas como [sem servidor das funções do Azure](http://azure.com/serverless)).

![BD do Azure do Cosmos ordenação arquitetura de referência de pipeline](./media/use-cases/event-sourcing.png)

Além disso, os dados armazenados na base de dados do Azure Cosmos podem ser integrados com o HDInsight para análise de macrodados através de tarefas do Apache Spark. Para obter detalhes sobre o conector do Spark para a BD do Cosmos do Azure, consulte [execute uma tarefa de Spark com a base de dados do Cosmos e o HDInsight](spark-connector.md).

## <a name="gaming"></a>Jogos
A camada de base de dados é um componente fundamental de aplicações de jogos. Jogos modernos executar gráfico processamento nos clientes móveis/consola, mas dependem da nuvem para fornecer conteúdo personalizado e personalizado, como no jogo estatísticas, integração de redes sociais e leaderboards de pontuação de alta. Jogos requerem frequentemente latências milissegundo único para leituras e escritas para fornecer um envolvimento no-jogo experiência. Uma base de dados jogo tem de ser rápido e ser capaz de processar grandes picos em taxas de pedidos durante novo inicia jogos e atualizações de funcionalidade.

BD do Azure do Cosmos é utilizado pelo jogos como [o nosso percurso inactivo: telefone não ataques Man](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) por [jogos seguinte](http://www.nextgames.com/), e [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). BD do Azure do Cosmos fornece as seguintes vantagens para os programadores de jogos:

* BD do Azure do Cosmos permite um desempenho ser ampliada ou reduzir aprovisionadas vertical. Isto permite jogos lidar com atualização perfil e estatísticas de dezenas para milhões de gamers simultâneas ao fazer uma única chamada de API.
* BD do Azure do Cosmos suporta milissegundo leituras e escritas para o ajudar a evitar quaisquer lags durante play jogo.
* A indexação automática do Azure DB de Cosmos permite a filtragem várias propriedades diferentes em tempo real, por exemplo, localize jogadores pelo respetivo leitor interno IDs ou os respetivos GameCenter, Facebook, Google IDs ou consulta com base na associação player um guild. Isto é possível sem criar indexar complexas ou de infraestrutura de fragmentação.
* Funcionalidades de redes sociais, incluindo mensagens de chat no jogo, as associações de guild player, desafios concluídos, leaderboards de pontuação de alta e gráficos de redes sociais são mais fáceis de implementar com um esquema flexível.
* BD do Azure do Cosmos como um gerido plataforma-como-um-serviço (PaaS) necessário requisitos mínimos de configuração e gestão de trabalho para permitir a iteração rápida e reduzir o tempo de colocação no mercado.

![Arquitetura de referência de jogos do Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplicações móveis e Web
BD do Azure do Cosmos costuma ser utilizada dentro de aplicações móveis e web e é adequado para a modelação interações sociais, integrar com os serviços de terceiros e de criação avançadas experiências personalizadas. Os SDKs de BD do Cosmos pode ser iOS avançado compilação utilizado e as aplicações Android com o populares [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplicações de redes sociais
É um caso de utilização comuns para a BD do Cosmos do Azure para armazenar e consultar o conteúdo de gerados pelo utilizador (UGC) para a web, aplicações móveis e de redes sociais suporte de dados. Alguns exemplos de UGC são sessões de chat, tweets, publicações no blogue, classificações e comentários. Muitas vezes, UGC em aplicações de redes sociais é um blend de texto de forma livre, propriedades, etiquetas e relações que não são tem um vínculo pela estrutura rigid. Conteúdo como chats, comentários e mensagens podem ser armazenados na base de dados do Cosmos sem necessidade de transformações ou o objeto complexo para as camadas de mapeamento relacional.  Propriedades de dados podem ser adicionadas ou modificadas facilmente para corresponder aos requisitos como programadores iterar código da aplicação, assim ao promover um desenvolvimento rápido.  

As aplicações que se integram com redes sociais de terceiros tem de responder para alterar os esquemas destas redes. Como dados automaticamente são indexados por predefinição na base de dados do Cosmos, dados estão prontos para ser consultados em qualquer altura. Por conseguinte, estas aplicações têm a flexibilidade para obter projeções de acordo com as respetivas necessidades.

Muitas das redes sociais aplicações executadas na escala global e podem apresentem um padrões de utilização imprevisíveis. Flexibilidade dimensionar o arquivo de dados é essencial como dimensiona camada da aplicação para corresponder a pedido de utilização.  Pode aumentar horizontalmente adicionando as partições de dados adicionais com uma conta de base de dados do Cosmos.  Além disso, também pode criar contas adicionais da BD do Cosmos em várias regiões. Para disponibilidade de região do serviço de base de dados do Cosmos, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

![Azure arquitetura de referência do Cosmos DB web app](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalização
Nowadays, as aplicações modernas são fornecidos com vistas complexas e experiências. Estes são normalmente dinâmicos, catering as preferências do utilizador ou moods e necessidades de imagem corporativa. Por conseguinte, as aplicações têm de ser capaz de obter as definições de personalizado eficazmente para compor elementos da IU e experiências rapidamente. 

JSON, um formato suportado pela base de dados do Cosmos, é um formato eficaz para representar os dados de esquema de IU como não só é simples, mas também pode ser facilmente interpretado pelo JavaScript. BD do cosmos oferece os níveis de consistência sincronizáveis que permitem operações de leitura rápidas com baixa latência escritas. Por conseguinte, a armazenar dados de esquema de IU, incluindo definições personalizadas como documentos JSON na base de dados do Cosmos é um meio eficaz para obter estes dados através da transmissão.

![Azure arquitetura de referência do Cosmos DB web app](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Passos seguintes
Para começar com Azure Cosmos DB, siga a nossa [inícios Rápidos](create-documentdb-dotnet.md), que explica como criar uma conta e a introdução à base de dados do Cosmos. 

Ou, se pretender ler mais sobre clientes que utilizam Cosmos DB, estão disponíveis os seguintes stories de cliente:

* [Jet.com](https://jet.com). Comércio challenger eyes o lugar para cima superior, é executado em nuvem da Microsoft, tira partido da BD do Cosmos uma escala global.
* [Asos.com](http://www.asos.com/). Asos.com é um arquivo de forma e beauty online British. Diversificado principalmente os adultos mais novo /, Asos sells mais 850 marcas, bem como o seu próprio intervalo clothing e acessórios.
* [Toyota](https://www.toyota.com/). Toyota Motor Corporation é um fabricante automotive japonês. Toyota aproveitadas a BD do Cosmos para uma aplicação de IoT global.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix desenvolvidas pela organização single-sign-on solução utilizando o Azure Service Fabric e da base de dados do Azure Cosmos
* [TEXA](https://customers.microsoft.com/story/texaspa) solução IoT revolutionary do TEXA para proprietários de vehicle ajuda a poupar tempo, dinheiro, gás — e possivelmente se encontra.
* [Pizza do Domino](https://www.dominos.com). Inc. de Pizza do Domino é uma cadeia de restaurante American pizza.
* [Controla a Johnson](http://www.johnsoncontrols.com). Controlos de Johnson é uma tecnologia diversified global e múltiplos industriais leader que serve uma vasta gama de clientes em mais de 150 países.
* [Microsoft Windows, loja Universal, IoT Hub do Azure, Xbox Live e outros serviços de Internet escala](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Como Microsoft baseia-se extremamente dimensionáveis serviços utilizando a BD do Cosmos do Azure.
* [Equipa do Microsoft Data e a análise](https://customers.microsoft.com/story/microsoftdataandanalytics). Da Microsoft, equipa de dados e análise obtém uma coleção de macrodados planet escala com base de dados do Azure Cosmos
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha utiliza BD do Cosmos do Azure para ligar a clientes e as empresas entre Índia.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit demora voo com base de dados do Azure Cosmos.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio muda de AWS BD do Cosmos do Azure para o conjunto de testes dados sociais à escala.
* [Em seguida jogos](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Mensagens de não Walking: soars Man do telefone jogo para #1 suportado pelo Azure Cosmos DB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Como Halo 5 implementado gameplay social utilizando a BD do Cosmos do Azure.
* [Galeria de análise da Cortana](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Análise de galeria da Cortana - um site da Comunidade dimensionável incorporado numa base de dados do Azure Cosmos.
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). À esquerda integrador dá-informações Global Multinational Firms em minutos com tecnologias de nuvem flexível.
* [República de notícias](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Adicionar intelligence para as notícias para fornecer informações com o objetivo de citizens acentuadas. 
* [SGS internacional](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Cor consistente em todo o mundo, de marcas principais ative a SGS. E SGS ativa para o Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Global leader Telenor utiliza a nuvem para mover com velocidade de um arranque. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). O arquivo do futuro é executado na pesquisa speedy e o fluxo de dados fácil.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Plataforma de software baseado no Azure divide barreiras as eficazes entre empresas e os clientes
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Fridge inteligente weka melhora a gestão de vaccine para que mais pessoas podem ser protegidas contra diseases
* [Tribes laranja](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Não há mais para essa aplicação prato que cumpra os olhos ou da boca.
* [Real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Real Madrid oferece o stadium próximo para milhões de 450 ventoinhas à volta de todo o mundo, com o Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU torna carro comprar diversão com a ajuda dos serviços do Azure
