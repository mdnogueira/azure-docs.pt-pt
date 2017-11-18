---
title: BD do Azure do Cosmos perguntas mais frequentes | Microsoft Docs
description: "Obtenha respostas às perguntas mais frequentes sobre a BD do Cosmos do Azure, um serviço de base de dados globalmente distribuída e múltiplos modelo. Saiba mais sobre a capacidade, níveis de desempenho e dimensionamento."
keywords: Perguntas de base de dados, perguntas mais frequentes perguntas, documentdb, azure, do Microsoft azure
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 2f46fc37b9050b19b83685c97198c29a5ce46289
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="azure-cosmos-db-faq"></a>FAQ do Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Noções básicas do Cosmos BD do Azure
### <a name="what-is-azure-cosmos-db"></a>O que é a base de dados do Azure Cosmos?
BD do Cosmos do Azure é um serviço de base de dados globalmente replicados e múltiplos modelo que oferece consultas avançadas sobre os dados sem esquema, ajuda a proporcionar um desempenho configurável e fiável e permite um desenvolvimento rápido. Se todos os alcançada através de uma plataforma gerida que é efetuada pela potência e alcance do Microsoft Azure. 

BD do Cosmos do Azure é a solução certa para a web, móveis, jogos, e aplicações IoT quando o débito previsível, elevada disponibilidade, latência baixa e um modelo de dados sem esquema são requisitos principais. Fornece flexibilidade de esquema e indexação avançada e inclui suporte de transacional com vários documento com JavaScript integrado. 

Para obter mais perguntas de base de dados, respostas e instruções para implementar e utilizar este serviço, consulte a [página de documentação do Azure Cosmos DB] ((https://docs.microsoft.com/azure/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>O que aconteceu ao DocumentDB?
A API do DocumentDB é um dos modelos de dados para a base de dados do Azure Cosmos e APIs suportadas. Além disso, base de dados do Azure Cosmos suporta, com Graph API (pré-visualização), a API de tabela e a API do MongoDB. Para obter mais informações, consulte [questões a partir de clientes do DocumentDB](#moving-to-cosmos-db).

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Como obter a minha conta do DocumentDB no portal do Azure?
No portal do Azure, clique no ícone de base de dados do Azure Cosmos no painel esquerdo. Se tiver uma conta DocumentDB, tem agora uma conta de base de dados do Azure Cosmos, sem qualquer alteração para a faturação.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos de utilização típica para a base de dados do Azure Cosmos?
BD do Cosmos do Azure é uma boa opção para o novo web, móveis, jogos e aplicações IoT onde o dimensionamento automático, o desempenho previsível, rápido ordem dos tempos de resposta de milissegundos e a capacidade de consultar sobre os dados sem esquema é importante. BD do Azure do Cosmos presta-se no desenvolvimento rápido e suportar a iteração contínua de modelos de dados de aplicação. As aplicações que gerem conteúdos gerados pelo utilizador e os dados são [casos de utilização comuns para a base de dados do Azure Cosmos](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como a base de dados do Azure Cosmos proporciona um desempenho previsível?
A [unidade de pedido](request-units.md) (RU) é a medida de débito do BD Azure Cosmos. Um débito de 1 RU corresponde ao débito do GET de um documento de 1 KB. Cada operação na BD do Cosmos do Azure, incluindo leituras, escritas, consultas SQL e execuções de procedimentos armazenados, tem um valor de RU determinista baseado no débito necessário para concluir a operação. Em vez de pensar CPU, memória e/s e como cada afetam o débito da sua aplicação, pode pensar em termos de uma medida RU único.

Pode reservar cada contentor de base de dados do Azure Cosmos com débito aprovisionado em termos de RUs de débito por segundo. Para aplicações de qualquer escala, pode referenciar pedidos individuais para medir os respetivos valores RU e aprovisionar um contentor para processar o total de unidades de pedido em todos os pedidos. Também pode aumentar verticalmente ou reduzir verticalmente o débito do contentor que as necessidades da sua aplicação evoluem. Para obter mais informações sobre unidades de pedido e para ajudar a determinar o contentor necessita, consulte [estimar necessidades de débito](request-units.md#estimating-throughput-needs) e tente a [Calculadora de débito](https://www.documentdb.com/capacityplanner). O termo *contentor* aqui refere-se para refere-se a uma coleção de API do DocumentDB, o gráfico da Graph API, a recolha de MongoDB API e a tabela de API de tabela. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Como a base de dados do Azure Cosmos suportam vários modelos de dados como chave/valor, columnar, documentos e o gráfico?

Chave/valor (tabela), columnar, documentos e dados de gráfico modelos são suportadas nativamente devido a ARS (átomos, os registos e sequências) design essa base de dados do Azure Cosmos estão incorporados no. Átomos, os registos e sequências de podem ser facilmente mapeadas e projetadas vários modelos de dados. As APIs para um subconjunto de modelos são direito disponível agora (DocumentDB, MongoDB, tabela e APIs de gráfico) e outras pessoas específicas para modelos de dados adicionais irão estar disponíveis no futuro.

BD do Azure do Cosmos tem um esquema agnóstico relativamente indexação motor com capacidade para indexar automaticamente todos os dados que ingere sem necessidade de qualquer esquema ou índices secundários do programador. O motor baseia-se num conjunto de esquemas de índice lógica (inverted, columnar, árvore) que desassociar o esquema de armazenamento do índice e subsistemas de processamento de consultas. BD do cosmos tem também a capacidade para suportar um conjunto de protocolos de transmissão e APIs de forma extensível e converte-os de forma eficiente para o modelo de dados principal (1) e as esquemas de índice lógica (2) tornando exclusivamente capaz de suportar vários modelos de dados de forma nativa.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB HIPAA está em conformidade?
Sim, base de dados do Azure Cosmos está em conformidade com a HIPAA. A HIPAA estabelece requisitos para a utilização, divulgação e salvaguarda das informações médicas pessoalmente identificáveis. Para obter mais informações, consulte o [Centro de Fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quais são os limites de armazenamento da base de dados do Azure Cosmos?
Não há nenhum limite para a quantidade total de dados que um contentor pode armazenar do BD Azure Cosmos.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quais são os limites de débito de base de dados do Azure Cosmos?
Não há nenhum limite para a quantidade total de débito que pode suportar um contentor do BD Azure Cosmos. A chave ideia é para distribuir a carga de trabalho aproximadamente uniformemente entre um suficientemente grande número de chaves de partição.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto custo base de dados do Azure Cosmos?
Para obter detalhes, consulte o [Azure Cosmos DB detalhes de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) página. Custos de utilização do Cosmos BD do Azure são determinados pelo número de contentores com aprovisionamento, o número de horas os contentores estiveram online e o débito aprovisionado para cada contentor. O termo *contentores* aqui refere-se a coleção de API do DocumentDB, gráfico da Graph API, recolha de API do MongoDB e tabelas de API de tabela. 

### <a name="is-a-free-account-available"></a>É uma conta gratuita disponível?
Sim, pode inscrever-uma conta de tempo limitado, sem encargos, sem compromissos. Para se inscrever, visite [tente BD do Azure do Cosmos gratuitamente](https://azure.microsoft.com/try/cosmosdb/) ou leia mais no [tente FAQ da BD do Azure Cosmos](#try-cosmos-db).

Se estiver familiarizado com o Azure, pode inscrever-se para obter um [conta gratuita do Azure](https://azure.microsoft.com/free/), que lhe oferece 30 dias e e um crédito para experimentar todos os serviços do Azure. Se tiver uma subscrição do Visual Studio, também são elegíveis para [livre créditos do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para utilizar em qualquer serviço Azure. 

Também pode utilizar o [emulador de BD do Azure Cosmos](local-emulator.md) para desenvolver e testar a aplicação localmente para gratuitamente, sem criar uma subscrição do Azure. Quando estiver satisfeito com a forma como a aplicação está a funcionar no emulador de BD do Cosmos do Azure, pode mudar para utilizar uma conta de base de dados do Azure Cosmos na nuvem.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Como posso obter ajuda adicional com o Azure Cosmos DB?
Se precisar de qualquer ajuda, entrar-no [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) ou [fórum MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), ou agendar um chat cara com a equipa de engenharia da BD do Cosmos Azure através do envio de correio a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com). 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Tente subscrições do Azure Cosmos DB

Podem agora desfrutar uma experiência de base de dados do Azure Cosmos limitado de tempo sem uma subscrição, de forma gratuita e compromissos. Para se inscrever para uma subscrição de tente DB Cosmos do Azure, aceda a [tente BD do Azure do Cosmos gratuitamente](https://azure.microsoft.com/try/cosmosdb/). Esta subscrição está separada do [avaliação gratuita do Azure](https://azure.microsoft.com/free/)e pode ser utilizado para além de uma versão de avaliação gratuita do Azure ou um Azure subscrição paga. 

Tente Azure Cosmos DB subscrições são apresentados no portal do Azure junto outras subscrições associadas à sua ID de utilizador. 

As seguintes condições aplicam-se a BD do Cosmos de Azure tente subscrições:

* Um contentor por subscrição para o SQL Server (API do DocumentDB), Gremlin (Graph API) e contas de tabela.
* Até 3 coleções por subscrição para contas do MongoDB.
* Capacidade de armazenamento de 10 GB.
* Replicação global está disponível a seguir [regiões do Azure](https://azure.microsoft.com/regions/): EUA Central, Europa do Norte e Sudeste asiático
* Débito máximo de 5 mil RU/s.
* Subscrições expiram após 24 horas e podem ser expandidas até um máximo de total de 48 horas.
* Não não possível criar pedidos de suporte do Azure para contas de tente BD do Azure Cosmos; No entanto, é fornecido suporte para subscritores com planos de suporte existentes. 

## <a name="set-up-azure-cosmos-db"></a>Configurar a base de dados do Azure Cosmos
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como posso inscrever-me para a base de dados do Azure Cosmos?
BD do Cosmos do Azure está disponível no portal do Azure. Em primeiro lugar, inscreva-se uma subscrição do Azure. Depois de se ter inscrito, pode adicionar uma API do DocumentDB, Graph API (pré-visualização), API de tabela ou conta de API do MongoDB à sua subscrição do Azure.

### <a name="what-is-a-master-key"></a>O que é uma chave mestra?
Uma chave mestra é um token de segurança para aceder a todos os recursos de uma conta. Indivíduos com a chave tem de leitura e escrita para todos os recursos na conta de base de dados. Tenha cuidado ao distribuir chaves mestras. A chave mestra principal e a chave mestra secundária estão disponíveis no **chaves** painel do [portal do Azure][azure-portal]. Para obter mais informações sobre chaves, consulte [ver, copiar e voltar a gerar acesso chaves](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quais são as regiões que PreferredLocations pode ser definido como? 
O valor de PreferredLocations pode ser definido como qualquer uma das regiões do Azure na qual a BD do Cosmos está disponível. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Existe nada que posso deve ter conhecimento ao distribuir dados por todo o mundo através de centros de dados do Azure? 
BD do Azure do Cosmos está presente em todas as regiões do Azure, conforme especificado no [regiões do Azure](https://azure.microsoft.com/regions/) página. Porque é o serviço de núcleo, cada novo Centro de dados tem uma presença de BD do Cosmos do Azure. 

Quando definir uma região, lembre-se de que o Azure Cosmos DB respeita nuvens sovereign e government. Ou seja, se criar uma conta numa região sovereign, não é possível replicar fora nessa região sovereign. Da mesma forma, não é possível ativar a replicação para outras localizações sovereign de uma conta externa. 

## <a name="develop-against-the-documentdb-api"></a>Desenvolver o documentdb de API

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>Como iniciar a programação com a API do DocumentDB?
Microsoft DocumentDB API está disponível no [portal do Azure][azure-portal]. Primeiro, deve inscrever-se uma subscrição do Azure. Depois de se inscrever para uma subscrição do Azure, pode adicionar o contentor de API do DocumentDB à sua subscrição do Azure. Para obter instruções sobre como adicionar uma conta de base de dados do Azure Cosmos, consulte [criar uma conta de base de dados de base de dados do Azure Cosmos](create-documentdb-dotnet.md#create-account). Se tiver uma conta DocumentDB no passado, tem agora uma conta de base de dados do Azure Cosmos. 

Existem [SDKs](documentdb-sdk-dotnet.md) disponíveis para .NET, Python, Node.js, JavaScript e Java. Os programadores também podem utilizar o [RESTful HTTP APIs](/rest/api/documentdb/) para interagir com recursos de BD do Cosmos Azure a partir de vários idiomas e plataformas.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Pode aceder alguns exemplos pronta a utilizar para obter um início de cabeçalho?
Exemplos para a API do DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md), e [Python](documentdb-python-samples.md) SDKs estão disponíveis no GitHub.


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>A base de dados do DocumentDB API suporta dados sem esquema?
Sim, a API do DocumentDB permite que as aplicações armazenem documentos JSON arbitrários sem definições de esquema ou de sugestões. Os dados estão imediatamente disponíveis para consulta através da interface de consulta de base de dados SQL do Azure Cosmos.  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>A API do DocumentDB suporta transações ACID?
Sim, a API do DocumentDB suporta transações entre documentos expressadas como procedimentos armazenados do JavaScript e é acionado. As transações estão confinadas a uma única partição dentro de cada coleção e executadas com semântica ACID como "todas ou nada," isolada de outros pedidos em execução simultânea de código e de utilizador. Se as exceções forem emitidas através da execução do lado do servidor do código da aplicação JavaScript, toda a transação é revertida. Para obter mais informações sobre transações, consulte [transações do programa de base de dados](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>O que é uma coleção?
Uma coleção é um grupo de documentos e a respetiva lógica da aplicação associada JavaScript. Uma coleção é uma entidade faturável, onde o [custo](performance-levels.md) é determinado pelo débito e utilizar o armazenamento. As coleções podem abranger uma ou mais partições ou servidores e podem Dimensionar para processar volumes praticamente ilimitados de armazenamento ou débito.

As coleções também são as entidades de faturação para Azure Cosmos DB. Cada coleção é faturada numa base horária, com base no débito aprovisionado e utilizada de espaço de armazenamento. Para obter mais informações, consulte [preços de base de dados do Azure Cosmos](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Como crio uma base de dados?
Pode criar bases de dados utilizando o [portal do Azure](https://portal.azure.com), conforme descrito nas [adicionar uma coleção](create-documentdb-dotnet.md#create-collection), um do [SDKs de BD do Azure Cosmos](documentdb-sdk-dotnet.md), ou o [REST APIs](/rest/api/documentdb/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Como posso configurar utilizadores e permissões?
Pode criar utilizadores e permissões utilizando um do [SDKs de API do Cosmos DB](documentdb-sdk-dotnet.md) ou [REST APIs](/rest/api/documentdb/).  

### <a name="does-the-documentdb-api-support-sql"></a>A API do DocumentDB suporta SQL?
O idioma de consulta SQL é um subconjunto avançado da funcionalidade de consulta que é suportado pelo SQL Server. O idioma de consulta de base de dados SQL do Azure Cosmos fornece os operadores avançados hierárquicos e relacionais e extensibilidade através de funções baseado em JavaScript, definido pelo utilizador (UDFs). A gramática JSON permite-lhe modelar documentos JSON como árvores connosco com nome, que são utilizados pelas técnicas de indexação automáticas BD do Cosmos do Azure e o dialeto de consulta SQL da base de dados do Azure Cosmos. Para obter informações sobre como utilizar a gramática do SQL Server, consulte o [QueryDocumentDB] [ query] artigo.

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>A API do DocumentDB suporta funções de agregação SQL?
A API do DocumentDB suporta a agregação de latência baixa em qualquer escala através de funções de agregação `COUNT`, `MIN`, `MAX`, `AVG`, e `SUM` através de gramática do SQL Server. Para obter mais informações, consulte [as funções de agregação](documentdb-sql-query.md#Aggregates).

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>Como a API do DocumentDB fornece simultaneidade?
A API do DocumentDB suporta o controlo de simultaneidade otimista (OCC) através de etiquetas de entidade HTTP ou ETags. Cada recurso DocumentDB API tem uma ETag e a ETag está definido no servidor de sempre que um documento é atualizado. O cabeçalho ETag e o valor atual são incluídas em todas as mensagens de resposta. Os etags são podem ser utilizados com o cabeçalho If-Match para permitir que o servidor decidir se deve ser atualizado um recurso. O valor de If-Match é o valor ETag ao ser verificado com base. Se o valor ETag corresponder o valor ETag de servidor, o recurso está atualizado. Se a ETag já não for atual, o servidor rejeita a operação com um "HTTP 412 Falha de pré-condição" código de resposta. Em seguida, o cliente obtém novamente o recurso para adquirir o valor ETag atual do recurso. Além disso, ETags pode ser utilizado com o cabeçalho If-None-Match para determinar se um novamente obtenção de um recurso necessário.

Para utilizar simultaneidade otimista no .NET, utilize o [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) classe. Para um exemplo de .NET, consulte [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) na amostra DocumentManagement no GitHub.

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>Como posso efetuar transações de API do DocumentDB?
A API do DocumentDB suporta transações de linguagem integrada através de acionadores e procedimentos armazenados do JavaScript. Todas as operações de base de dados no interior de scripts são executadas em isolamento do instantâneo. Se se tratar de uma coleção de partição única, a execução é confinada à coleção. Se a coleção estiver particionada, a execução é confinada aos documentos com o mesmo valor de chave de partição dentro da coleção. Um instantâneo das versões do documento (ETags) é tirado no início da transação e consolidado apenas se o script for concluído com êxito. Se o JavaScript emitir um erro, a transação é revertida. Para obter mais informações, consulte [programação JavaScript do lado do servidor de base de dados do Azure Cosmos](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Como pode posso inserção em massa documentos para a base de dados do Cosmos?
-Pode inserção em massa documentos para a base de dados do Azure Cosmos em qualquer uma das seguintes formas:

* A ferramenta de migração de dados, conforme descrito em [ferramenta de migração de base de dados para a base de dados do Azure Cosmos](import-data.md).
* Procedimentos armazenados, conforme descrito em [programação JavaScript do lado do servidor de base de dados do Azure Cosmos](programming.md).

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>É a API do DocumentDB suporte recurso ligação colocação em cache?
Sim, porque a BD do Cosmos do Azure é um serviço RESTful, ligações de recursos são imutáveis e podem ser colocadas em cache. Os clientes do DocumentDB API podem especificar um cabeçalho de "If-None-Match" para leituras em relação a quaisquer recursos como o documento ou coleção e, em seguida, Atualize as respetivas cópias locais depois da versão do servidor foi alterado.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>Uma instância local da API do DocumentDB está disponível?
Sim. O [emulador de BD do Azure Cosmos](local-emulator.md) fornece uma emulação de alta-fidelidade do serviço base de dados do Cosmos. Suporta a funcionalidade que é idêntica ao Cosmos BD do Azure, incluindo suporte para criar e consultar documentos JSON, aprovisionamento e dimensionamento coleções e a execução de acionadores e procedimentos armazenados. Pode desenvolver e testar aplicações utilizando o emulador de BD do Cosmos do Azure e implementá-las para o Azure uma escala global, fazendo uma configuração única alterar para o ponto final de ligação de base de dados do Azure Cosmos.

## <a name="develop-against-the-api-for-mongodb"></a>Desenvolver com a API para MongoDB
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>O que é a API de BD do Cosmos do Azure para o MongoDB?
A API de BD do Cosmos do Azure para o MongoDB é uma camada de compatibilidade, que permite que aplicações facilmente e transparente comunicar com o motor de base de dados do Azure Cosmos DB nativo, utilizando existente, suportada de Comunidade Apache MongoDB APIs e os controladores. Os programadores podem agora utilizar cadeias de ferramenta MongoDB existentes e competências para criar aplicações que tirar partido da BD do Cosmos do Azure. Os programadores beneficiam as capacidades exclusivas de BD do Cosmos do Azure, que incluem a indexação automática, cópia de segurança de manutenção, contratos de nível de serviço de cópia de financially (SLAs) e assim sucessivamente.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Como ligar a minha API para a base de dados de MongoDB?
A forma mais rápida para ligar à API de BD do Cosmos do Azure para o MongoDB é a principal para o [portal do Azure](https://portal.azure.com). Aceda à sua conta e, em seguida, no menu de navegação esquerdo, clique em **início rápido**. Início rápido é a melhor forma de obter fragmentos de código para ligar à base de dados. 

BD do Azure do Cosmos impõe requisitos de segurança estritos e normas. As contas do Azure do Cosmos DB requerem comunicação segura através de SSL e autenticação, pelo que não se esqueça de utilizar TLSv1.2.

Para obter mais informações, consulte [ligar à sua API para a base de dados de MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Existem códigos de erro adicionais para uma API para a base de dados de MongoDB?
Para além de códigos de erro comuns do MongoDB, a API do MongoDB tem os seus próprios códigos de erro específico:


| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de pedido consumido excedeu a taxa de aprovisionamento de unidade de pedido para a coleção e foi limitado. | Considere aumentar o débito da coleção do portal do Azure ou tentar novamente. |
| ExceededMemoryLimit | 16501 | Como um serviço de multi-inquilino, a operação excedeu a alocação de memória do cliente. | Reduzir o âmbito da operação através de critérios de consulta mais restritivas ou contacte o suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Exemplo:  *&nbsp; &nbsp; &nbsp; &nbsp;db.getCollection('users').aggregate ([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {nome: "Andy"}}, <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$sort: {idade: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api"></a>Desenvolver com a tabela de API

### <a name="how-can-i-use-the-table-api-offering"></a>Como posso utilizar a oferta de API de tabela? 
A API de tabela de base de dados do Azure Cosmos está disponível no [portal do Azure][azure-portal]. Primeiro, deve inscrever-se uma subscrição do Azure. Depois de se ter inscrito, pode adicionar uma conta de API de tabela de base de dados do Azure Cosmos à sua subscrição do Azure e, em seguida, adicione as tabelas para a sua conta. 

Pode encontrar os idiomas suportados e associado rápida-inicia no [introdução à API de tabela de base de dados do Azure Cosmos](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>É necessário um novo SDK para utilizar a API de tabela? 
Não, existentes armazenamento que SDKs devem continuarão a funcionar. Contudo, recomenda-se que um sempre obtém os SDKs mais recentes para o melhor suporte e em muitos casos desempenho superior. Ver a lista dos idiomas disponíveis no [introdução à API de tabela de base de dados do Azure Cosmos](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Em que a API de tabela não é idêntico com o comportamento de armazenamento de tabelas do Azure?
Existem algumas diferenças de comportamento utilizadores provenientes da Table storage do Azure que pretendem criar tabelas com a API de tabela de base de dados do Azure Cosmos devem ter conhecimento:

* API de tabela de base de dados do Azure Cosmos utiliza um modelo de capacidade reservado para garantir o desempenho garantido, mas isto significa que um pays para a capacidade, assim que a tabela é criada, mesmo que a capacidade não está a ser utilizada. Table Storage do Azure um apenas pays para a capacidade, na verdade, está a ser utilizada. Esta ajuda a explicar por que motivo API de tabela pode oferecer que um 10 ms ler e a 15 ms escrever SLA no percentil 99th Table storage do Azure oferece um SLA de segundo 10. Mas como consequence, com as tabelas de API de tabela, mesmo vazios tabelas sem quaisquer pedidos, custo dinheiro para garantir que a capacidade está disponível para processar pedidos aos mesmos, o SLA oferecidas pelo Azure Cosmos DB.
* Resultados devolvidos pela API de tabela não são ordenados por ordem de chave de linha/chave de partição conforme forem no Table storage do Azure.
* Chaves de linha só podem ser até 255 bytes
* Chamadas de CreateIfNotExists estão limitadas por uma limitação de gestão que seja fixo e separada das outras operações de tabela que estão abrangidas pelos RUs. Isto significa que os efetuar grandes quantidades de CreateIfNotExists obterem limitados e irão não será capazes de fazer nada sobre o assunto, porque o limite não provém do respetivos RUs.
* Não é atualmente suportado CORS
* Os nomes de tabela no armazenamento de Azure Table não são maiúsculas e minúsculas, mas estão na API de tabela de base de dados do Azure Cosmos

Em termos da API REST, existem várias opções de pontos finais/consulta que não são suportadas pelo Azure Cosmos DB tabela API de:
| Método de REST | Opção de consulta/ponto final de REST | URLs do documento | Explicação |
| ------------| ------------- | ---------- | ----------- |
| OBTER, COLOCAR | /? restype =service@comp= propriedades| [Definir as propriedades do serviço tabela](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) e [obter propriedades de serviço tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Este ponto final é utilizado para definir as regras CORS, configuração de armazenamento da análise e as definições de registo. CORS não é atualmente suportada e registo e análise são processadas forma diferente na base de dados do Azure Cosmos que tabelas de armazenamento do Azure |
| OPÇÕES | / < nome de recurso de tabela > | [Pré-voo CORS tabela pedido](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Isto faz parte de CORS que BD do Cosmos Azure não suporta atualmente. |
| INTRODUÇÃO | /? restype =service@comp= estatísticas | [Obter estatísticas de serviço tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Fornece informações como rapidamente dados está a replicar entre principais e secundárias. Isto não é necessário na base de dados do Cosmos como a replicação faz parte das escritas. |
| OBTER, COLOCAR | /MyTable? concluída = acl | [Obter tabela ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) e [Definir tabela ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Este obtém e define as políticas de acesso armazenada utilizadas para gerir assinaturas de acesso partilhado (SAS). Embora seja suportado SAS, estão definidas e geridos de forma diferente. |

Além disso API de tabela de base de dados do Azure Cosmos só suporta o formato JSON, não ATOM.

Enquanto a base de dados do Azure Cosmos suporta acesso partilhado assinaturas (SAS) existem determinadas políticas não suporta, especificamente os relacionados com operações de gestão, tais como o direito de criar novas tabelas.

Para o SDK .NET em particular, existem algumas classes e métodos que BD do Cosmos Azure não suporta atualmente.

| Classe | Método não suportado |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | SetPermissions * |
|            | GetPermissions * |
| TableServiceContext | * (esta classe é realmente preterida) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Se qualquer um destas diferenças são um problema para o seu projeto contacte [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) e informe-nos.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como posso fornecer comentários sobre o SDK ou erros?
Pode partilhar os seus comentários em qualquer uma das seguintes formas:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [Fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>O que é a cadeia de ligação que tenho de utilizar para ligar à API de tabela?
A cadeia de ligação é:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.table.cosmosdb.azure.com
```
Pode obter a cadeia de ligação da página de cadeia de ligação no portal do Azure. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Como posso substituir as definições de configuração para as opções de pedido no SDK do .NET para a API de tabela?
Para informações sobre definições de configuração, consulte [capacidades de base de dados do Azure Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Algumas definições são processadas no método CreateCloudTableClient e outros através do App. config na secção appSettings a aplicação de cliente.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existem alterações para os clientes que utilizam o armazenamento de tabelas do Azure SDKs existente?
nenhum. Não foram efetuadas alterações para os clientes de novas ou existentes que estão a utilizar o armazenamento de tabelas do Azure existente SDKs. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Como posso ver dados de tabela que são armazenados na base de dados do Azure Cosmos para utilização com a API de tabela? 
Pode utilizar o portal do Azure para procurar os dados. Também pode utilizar o código de API de tabela ou as ferramentas mencionadas na resposta seguinte. 

### <a name="which-tools-work-with-the-table-api"></a>As ferramentas de trabalham com a API de tabela? 
Pode utilizar o [Explorador de armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Ferramentas com a flexibilidade de tirar uma cadeia de ligação no formato especificado anteriormente podem suportar a nova API de tabela. É fornecida uma lista das ferramentas de tabela no [ferramentas de cliente de armazenamento do Azure](../storage/common/storage-explorers.md) página. 

### <a name="do-powershell-or-azure-cli-work-with-the-table-api"></a>PowerShell ou a CLI do Azure funcionam com a API de tabela?
Não há suporte para [PowerShell](table-powershell.md). Suporte da CLI do Azure não está atualmente disponível.

### <a name="is-the-concurrency-on-operations-controlled"></a>É a simultaneidade operações controlada?
Sim, a simultaneidade otimista é fornecida através da utilização de mecanismo a ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>O modelo de consulta de OData é suportado para entidades? 
Sim, a API de tabela suporta a consulta de OData e consultas LINQ. 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Pode ligar ao Table Storage do Azure e a API de tabela de base de dados do Azure Cosmos lado a lado na mesma aplicação? 
Sim, pode ligar ao criar duas instâncias separadas do CloudTableClient, cada apontar para o seu próprio URI através da cadeia de ligação.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Como posso migrar uma aplicação de armazenamento de tabelas do Azure existente esta oferta?
[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) e [ferramenta de migração de dados do Azure Cosmos DB](import-data.md) são ambas suportada.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Como é expansão de tamanho de armazenamento feito para este serviço se, por exemplo, iniciar com  *n*  GB de dados e os meus dados irá aumentar 1 TB ao longo do tempo? 
BD do Azure do Cosmos foi concebida para fornecer armazenamento ilimitado através da utilização de dimensionamento horizontal. O serviço pode monitorizar e aumentar eficazmente o seu armazenamento. 

### <a name="how-do-i-monitor-the-table-api-offering"></a>Como monitorizar a oferta de API de tabela?
Pode utilizar a API de tabela **métricas** painel para monitorizar os pedidos e utilização do armazenamento. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Como calcular o débito que exigir?
Pode utilizar o estimator de capacidade para calcular o TableThroughput necessárias para as operações. Para obter mais informações, consulte [armazenamento de dados e unidades de pedido de estimativa](https://www.documentdb.com/capacityplanner). Em geral, pode representar entidade como JSON e fornecer os números para as operações. 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Posso utilizar o SDK de API de tabela localmente com o emulador?
Neste momento, não.

### <a name="can-my-existing-application-work-with-the-table-api"></a>A minha aplicação existente pode trabalhar com a API de tabela? 
Sim, a API do mesma é suportada.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>É necessário migrar as minhas aplicações de armazenamento de tabelas do Azure existentes para o SDK se não pretender utilizar as funcionalidades de API de tabela?
Não, pode criar e utilizar ativos de armazenamento de tabelas do Azure existentes sem interrupção de qualquer tipo. No entanto, se utilizar a API de tabela, não podem beneficiar do índice automático, a opção de consistência adicionais ou distribuição global. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>Como adicionar replicação dos dados na tabela API em várias regiões do Azure?
Pode utilizar o portal do Azure Cosmos DB [as definições de replicação global](tutorial-global-distribution-documentdb.md#portal) adicionar regiões que são adequadas para a sua aplicação. Para desenvolver uma aplicação distribuída global, deve também adicionar a sua aplicação com as informações de PreferredLocation definidas para a região local para fornecer a leitura de latência baixa. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Como posso alterar a região primária escrita para a conta na API tabela?
Pode utilizar o painel de portal de replicação global de BD do Cosmos do Azure para adicionar uma região e, em seguida, efetuar a ativação pós-falha para a região necessária. Para obter instruções, consulte [desenvolver com contas de base de dados do Azure Cosmos multirregião](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como configurar o meu regiões leitura preferenciais de latência baixa quando posso distribuir os meus dados? 
Para ajudar a ler a partir da localização local, utilize a chave de PreferredLocation no ficheiro App. config. Para as aplicações existentes, a API de tabela emitir um erro se LocationMode está definido. Remova esse código, porque a API de tabela escolherá estas informações a partir do ficheiro App. config. Para obter mais informações, consulte [capacidades de base de dados do Azure Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Como necessário pensar sobre os níveis de consistência na tabela API? 
BD do Azure do Cosmos fornece bem reasoned compromissos entre consistência, disponibilidade e a latência. BD do Azure do Cosmos oferece cinco níveis de consistência para os programadores de API de tabela, para que possa escolher o modelo de consistência direito ao nível da tabela e fazer pedidos individuais ao consultar os dados. Quando um cliente se liga, pode especificar um nível de consistência. Pode alterar o nível através do argumento consistencyLevel CreateCloudTableClient. 

A API de tabela fornece a latência baixa lê com "ler as seus próprios escritas," com a consistência vinculada Bounded como predefinição. Para obter mais informações, consulte [níveis de consistência](consistency-levels.md). 

Por predefinição, o Table storage do Azure fornece a consistência forte numa região e consistência Eventual nas localizações secundárias. 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>API de tabela de base de dados do Azure Cosmos oferecem mais níveis de consistência ao Table storage do Azure?
Sim, para obter informações sobre como beneficiar a natureza distribuída do Azure Cosmos DB, consulte o artigo [níveis de consistência](consistency-levels.md). Porque garantias são fornecidas para os níveis de consistência, pode utilizá-los com confiança. Para obter mais informações, consulte [capacidades de base de dados do Azure Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global está ativada, quanto tempo demora a replicar os dados?
BD do Azure do Cosmos consolida os dados de forma durável na região local e envia os dados para outras regiões imediatamente num fim de milissegundos. Esta replicação está dependente apenas na hora reportadas round-trip (RTT) do Centro de dados. Para saber mais sobre a capacidade de distribuição global do Azure Cosmos DB, consulte o artigo [BD do Cosmos do Azure: um serviço de base de dados globalmente distribuída no Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>É possível alterar o nível de consistência de pedido de leitura?
Com base de dados do Azure Cosmos, pode definir o nível de consistência ao nível do contentor (na tabela). Ao utilizar o SDK .NET, pode alterar o nível de fornecer o valor para a chave de TableConsistencyLevel no ficheiro App. config. Os valores possíveis são: forte, consistência vinculada, sessão, prefixo consistente e Eventual. Para obter mais informações, consulte [níveis de consistência Sincronizáveis dados na base de dados do Azure Cosmos](consistency-levels.md). A chave ideia é que não é possível definir a consistência de pedido nível em mais do que a definição da tabela. Por exemplo, não é possível definir o nível de consistência para a tabela Eventual e o nível de consistência do pedido em forte. 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Como a API de tabela processar ativação pós-falha se uma região ficar inativo? 
A API de tabela tira partido da plataforma global distribuída da base de dados do Azure Cosmos. Para garantir que a aplicação pode tolerar o período de indisponibilidade do Centro de dados, ativar, pelo menos, uma região mais para a conta no portal do Azure Cosmos DB [desenvolver com contas de base de dados do Azure Cosmos multirregião](regional-failover.md). Pode definir a prioridade da região utilizando o portal [desenvolver com contas de base de dados do Azure Cosmos multirregião](regional-failover.md). 

Pode adicionar tantos regiões à medida que pretende para a conta e controla em que pode efetuar a ativação pós-falha, fornecendo uma prioridade de ativação pós-falha. Obviamente, para utilizar a base de dados, tem de fornecer uma aplicação não existe demasiado. Se o fizer, os seus clientes não verificará o período de indisponibilidade. O [cliente mais recente do .NET SDK](table-sdk-dotnet.md) é automática homing, mas outros SDKs não estão. Ou seja, que pode detetar a região que está inativo e automaticamente a ativação pós-falha para a região de novo.

### <a name="is-the-table-api-enabled-for-backups"></a>A API de tabela está ativada para cópias de segurança?
Sim, a API de tabela tira partido da plataforma de base de dados do Azure Cosmos para cópias de segurança. As cópias de segurança são efetuadas automaticamente. Para obter mais informações, consulte [cópia de segurança Online e de restauro com base de dados do Azure Cosmos](online-backup-and-restore.md).

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>A API de tabela de índice todos os atributos de uma entidade por predefinição?
Sim, todos os atributos de uma entidade são indexados por predefinição. Para obter mais informações, consulte [BD do Azure Cosmos: indexação políticas](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Esta média não é necessário criar vários índices para satisfazer as consultas? 
Sim, a API de tabela de base de dados do Azure Cosmos fornece a indexação automática de todos os atributos sem qualquer definição de esquema. Esta automatização liberta os programadores concentrar-se na aplicação, em vez de na gestão e criação de índices. Para obter mais informações, consulte [BD do Azure Cosmos: indexação políticas](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Pode alterar a política de indexação?
Sim, pode alterar a política de indexação fornecendo a definição do índice. Para obter mais informações, consulte [capacidades de base de dados do Azure Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Terá de corretamente codificar e as definições de escape. 

para o não - .NET SDKs a política de indexação só pode ser definida no portal em **Explorador de dados**, navegue para a tabela específica que pretende alterar e, em seguida, aceda ao **definições de dimensionamento &**-> política de indexação, Efetue a alteração pretendida e, em seguida, **guardar**.

Do .NET SDK podem ser submetido no ficheiro App. config:
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>BD do Azure do Cosmos como uma plataforma parece ter muita das capacidades, tal como a ordenação, os agregados, hierarquia e outras funcionalidades. Será possível adicionar estas capacidades para a API de tabela? 
A API de tabela fornece a mesma funcionalidade de consulta que o Table storage do Azure. BD do Azure do Cosmos também suporta a ordenação, os agregados, geoespacial consulta, hierarquia e uma vasta gama de funções incorporadas. Podemos irá fornecer funcionalidades adicionais na API tabela numa atualização futura do serviço. Para obter mais informações, consulte [as consultas SQL para a API de DocumentDB do Azure Cosmos DB](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando posso alterar TableThroughput para a API de tabela?
Deve alterar TableThroughput quando qualquer uma das seguintes condições aplicam-se:
* Se estiver a efetuar uma extração, transformação e carregamento (ETL) de dados ou que pretende carregar uma grande quantidade de dados num curto período de tempo. 
* Precisa de mais débito do contentor no back-end. Por exemplo, verá que o débito utilizado mais do que o débito aprovisionado e pode obter limitadas. Para obter mais informações, consulte [débito de conjunto para contentores de base de dados do Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Pode aumentar verticalmente ou reduzir verticalmente o débito da minha tabela de API de tabela? 
Sim, pode utilizar o painel de escala do portal do Azure Cosmos DB para aumentar o débito. Para obter mais informações, consulte [conjunto débito](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>É uma predefinição que tablethroughput definida para tabelas recentemente aprovisionadas?
Sim, se não substituir TableThroughput através de App. config e não utilizar um contentor previamente criado na base de dados do Azure Cosmos, o serviço cria uma tabela com débito de 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>É efetuada qualquer alteração de preço para clientes existentes do serviço de armazenamento de tabelas do Azure?
nenhum. Não há nenhuma alteração no preço para clientes de armazenamento de tabelas do Azure existentes. 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Como é o preço calculado para a API de tabela? 
O preço depende o TableThroughput alocado. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-offering"></a>Como processar qualquer limitação em tabelas oferta de API de tabela? 
Se a velocidade do pedido excede a capacidade de débito aprovisionado para o contentor subjacente, receberá um erro e o SDK repete a chamada por aplicar a política de repetição.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Por que motivo é necessário escolher um débito para além dos PartitionKey e RowKey para tirar partido da oferta de API de tabela da base de dados do Azure Cosmos?
BD do Azure do Cosmos define um débito predefinido para o contentor se não fornecer uma no ficheiro App. config ou através do portal. 

BD do Azure do Cosmos fornece garantias de desempenho e a latência, com os limites superior a operação. Este garantia é possível quando o motor pode impor governação nas operações do inquilino. Definição TableThroughput garante que obtém o garantida débito e latência, porque a plataforma de reserva esta capacidade e garantias de êxito operacional. 

Ao utilizar a especificação de débito, aprovisionadas que pode alterá-lo para beneficiar da sazonalidade da sua aplicação, para satisfazer as necessidades de débito e reduzir os custos.

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Table storage do Azure foi muito económico para me permitir, porque posso pagar apenas para armazenar os dados e posso raramente consulta. A oferta de API de tabela de base de dados do Azure Cosmos parece ser charging-me, apesar de não efetuar uma única transação ou armazenados nada. Pode,. explicam?

BD do Azure do Cosmos foi concebido para ser um sistema global distribuído, com base no SLA com garantias de disponibilidade, débito e latência. Quando reservar débito do BD Azure Cosmos, é garantido, ao contrário do débito de outros sistemas. BD do Cosmos do Azure fornece funcionalidades adicionais que os clientes pedidas, como índices secundários e distribuição global.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nunca é apresentada uma notificação de "quota completo" (indica que uma partição é completa) quando posso ingerir dados no Table storage do Azure. Com a API de tabela, é apresentada esta mensagem. É esta a oferta limitando-me e forçar-me para alterar a minha aplicação existente?

BD do Cosmos do Azure é um sistema com base no SLA que fornece o dimensionamento ilimitado, com garantias de latência, débito, disponibilidade e consistência. Para garantir o desempenho premium garantido, certifique-se de que o tamanho dos dados e índice são geríveis e dimensionável. O limite de 10 GB no número de entidades ou os itens por chave de partição é garantir que fornecemos um excelente desempenho de consulta e de pesquisa. Para se certificar de que a aplicação dimensiona bem, mesmo para o Storage do Azure, recomendamos que lhe *não* criar uma partição quente ao armazenar todas as informações na partição de um e -lo a consultar. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Por isso, PartitionKey RowKey é ainda necessário e com a API de tabela? 
Sim. Como a área de superfície da API de tabela é semelhante à que o Table storage do Azure SDK, a chave de partição fornece uma forma eficaz para distribuir os dados. A chave de linha é exclusiva dentro dessa partição. A chave da fila tem de estar presente e não pode ser nula como o SDK padrão. O comprimento do RowKey é 255 bytes e o comprimento do PartitionKey é de 1 KB. 

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quais são as mensagens de erro para a API de tabela?
O Table storage do Azure e de API de tabela de base de dados do Azure Cosmos utilizam os SDKs do mesmos, de modo a maioria dos erros será a mesma.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Por que motivo posso obter limitado ao tentar criar muita das tabelas umas a seguir a API de tabela?
BD do Cosmos do Azure é um sistema com base no SLA que fornece a latência, débito, disponibilidade e garantias de consistência. Porque é um sistema aprovisionado, reserva recursos para garantir estes requisitos. A taxa rápida de criação de tabelas é detetada e limitada. Recomendamos que observe a taxa de criação de tabelas e reduzi-lo para menos de 5 por minuto. Lembre-se de que a API de tabela é um sistema aprovisionado. Neste momento que aprovisionar, começará pagar para o mesmo. 

## <a name="develop-against-the-graph-api-preview"></a>Desenvolver contra a Graph API (pré-visualização)
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Como pode aplicar a funcionalidade da Graph API (pré-visualização) à base de dados do Azure Cosmos?
Pode utilizar uma biblioteca de extensão para aplicar a funcionalidade da Graph API (pré-visualização). Esta biblioteca é chamada gráficos do Microsoft Azure e está disponível no NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Parece que suporta o idioma de transversal Gremlin gráfico. Planear adicionar mais formas de consulta?
Sim, planeamos adicionar outros mecanismos para a consulta no futuro. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>Como posso utilizar a nova oferta de Graph API (pré-visualização)? 
Para começar a utilizar, execute o [Graph API](../cosmos-db/create-graph-dotnet.md) artigo de início rápido.

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>Desenvolver com a API de Cassandra Apache (pré-visualização)

### <a name="what-is-the-protocol-version-supported-in-the-private-preview-is-there-a-plan-to-support-other-protocols"></a>O que é a versão do protocolo suportada na pré-visualização privada? Existe um plano para suportar outros protocolos?
Apache Cassandra API para a base de dados do Azure Cosmos suporta hoje versão CQL 4. Se tiver comentários sobre o suporte de outros protocolos, indique através de [comentários do uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Porquê escolhe um débito para uma tabela um requisito?
BD do Azure do Cosmos define débito predefinido para o contentor com base no onde criar a tabela a partir de - portal ou CQL. BD do Azure do Cosmos fornece garantias de desempenho e a latência, com os limites superior a operação. Este garantia é possível quando o motor pode impor governação nas operações do inquilino. Débito de definição garante que obtém o garantida débito e latência, porque a plataforma de reserva esta capacidade e garantias de êxito da operação. Pode alterar aprovisionadas débito para beneficiar da sazonalidade da sua aplicação e reduzir os custos.

O conceito de débito é explicado no [unidades de pedido na base de dados do Azure Cosmos](request-units.md) artigo. O débito para uma tabela igualmente é distribuído entre as partições físicas subjacentes.  

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>O que é a predefinição RU/s da tabela quando criado através da CQL? E se necessário alterá-la?
BD do Azure do Cosmos utiliza unidades de pedido por segundo (RU/s) como uma moeda para fornecer o débito. As tabelas criadas através de CQL ter 400 RU. Pode alterar o RU do portal. 

CQL
```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET
```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload); 
``` 

### <a name="what-happens-when-throughput-is-exceeded"></a>O que acontece quando o débito é excedido? 
BD do Azure do Cosmos fornece garantias de desempenho e a latência, com os limites superior a operação. Este garantia é possível quando o motor pode impor governação nas operações do inquilino. Isto é possível com base na definição o débito, que garante que obtém o garantida débito e latência, porque esta capacidade de reservas de plataforma e garantias de êxito da operação. Quando exceder esta capacidade obter a mensagem de erro sobrecarregado que indica que a capacidade foi excedida. 0x1001 sobrecarregado: não é possível processar o pedido porque "Taxa de pedidos é grande". Neste juncture é essencial para ver as operações e as respetivas volume faz com que este problema. Pode obter uma ideia sobre a capacidade foram consumida exceder a capacidade aprovisionada com métricas no portal. Em seguida, certifique-se a capacidade é consumida quase igualmente em todas as partições subjacentes. Se vir mais do que o débito é consumido por uma partição, tiver dissimetrias da carga de trabalho. 

Métricas estão disponíveis que mostram-lhe como débito é utilizado durante horas, dias e por sete dias, em partições ou num agregado. Para obter mais informações, consulte [monitorização e a depuração com métricas do BD Azure Cosmos](use-metrics.md).

Os registos de diagnóstico serão explicados de forma a [registo de diagnóstico de base de dados do Azure Cosmos](logging.md) artigo.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>É o mapa de chave primário para o conceito de chave de partição da base de dados do Azure Cosmos?
Sim, a chave de partição é utilizada para colocar a entidade na localização correta. Do BD Azure Cosmos é utilizado para localizar a partição direito lógica que está armazenada numa partição física. O conceito de criação de partições também é explicado no [partição e o dimensionamento do BD Azure Cosmos](partition-data.md) artigo. O essencial tirar ausente aqui é que uma partição lógica não deve exceder o limite de 10 GB hoje. 

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>O que acontece quando é apresentada uma notificação de "quota completa", que indica que uma partição está cheia?
BD do Cosmos do Azure é um sistema com base no SLA ilimitada escala, fornece garantias de latência, débito, disponibilidade e consistência. API do Cassandra demasiado permite que o armazenamento ilimitado de dados. Este armazenamento ilimitado baseia-se na horizontal scaleout de dados utilizando a criação de partições, como o conceito de chave. O conceito de criação de partições também é explicado no [partição e o dimensionamento do BD Azure Cosmos](partition-data.md) artigo.

O limite de 10 GB no número de entidades ou itens por partição lógica deve cumprir. Para garantir que a aplicação dimensiona bem, é recomendável que lhe *não* criar uma partição quente ao armazenar todas as informações na partição de um e -lo a consultar. Este erro pode apenas provenientes se que dados é skewed - que está a ter muitos dados para a chave de uma partição - ou seja, mais de 10 GB. Pode encontrar a distribuição de dados utilizando o portal de armazenamento. Forma para corrigir este erro é recrete a tabela e escolha um primário granular (chave de partição), que permite uma melhor distribuição dos dados.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>É possível utilizar Cassandra API como arquivo de valor de chave com milhões ou billions das chaves de partição individuais?
BD do Azure do Cosmos pode armazenar dados ilimitados ao aumentar horizontalmente o armazenamento. Este facto é independente do débito. Sim sempre apenas pode utilizar a API de Cassandra para armazenar e obter chaves/valores ao especificar a chave de partição/primário à direita. Estas chaves individuais obter a sua própria partição lógica e manter-se visível partição física sem problemas. 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>É possível criar várias tabelas com Apache Cassandra API do Cosmos BD do Azure?
Sim, é possível crete várias tabelas com Apache Cassandra API. Cada um nessas tabelas é tratada como unidade de débito e armazenamento. 

### <a name="is-it-possible-to-create-multiple-tables-in-succession"></a>É possível criar várias tabelas sucessivamente?
BD do Azure do Cosmos é recursos regido para atividades de plane de dados e controlo. Contentores, como coleções, as tabelas são entidades de tempo de execução que sejam aprovisionadas para fornecido a capacidade de débito. A criação destes contentores sucessivamente rápida não é esperado de atividade e limitadas. Se tiver testes que remova/criar tabelas imediatamente - tente espaço-los de.

### <a name="what-is-maximum-number-of-tables-which-can-be-created"></a>O que é o número máximo de tabelas que podem ser criados?
Não há nenhum físico limite no número de tabelas, envie uma mensagem de e-mail em [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) se tiver um grande número de tabelas (onde o tamanho total gradual excede 10 TB de dados) que têm de ser criada a partir de habitual 10s ou 100s. 

### <a name="what-is-the-maximum--of-keyspace-which-we-can-create"></a>O que é o n. º máximo de keyspace que podemos criar? 
Não há nenhum limite no número de keyspaces físico conforme forem contentores de metadados, envie uma mensagem de e-mail em [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) se tiver um grande número de keyspaces por algum motivo. 

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>É possível colocar em grande quantidade de dados depois de iniciar a partir da tabela normal? 
A capacidade de armazenamento é gerida automaticamente e aumenta pois push no mais dados. Por isso, pode importar confidencialidade como a quantidade de dados precisar, sem gestão e o aprovisionamento de nós, etc. 

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>É possível fornecer yaml ficheiro definições para configurar o comportamento do Apache Casssandra API do Cosmos BD do Azure?
Apache Cassandra API do Cosmos BD do Azure é um serviço de plataforma. Fornece compatibilty de nível de protocolo para executar operações. Oculta, ausente, a complexidade da gestão, monitorização e configuração. Como um programador/utilizador não terá de preocupar com a disponibilidade, tombstones, cache da chave, a cache de linha, filtro bloom e sem-número de outras definições. Vai Apache Cassandra API do Azure DB de Cosmos fornecer leitura e escrita desempenho que necessita sem a sobrecarga de configuração e gestão.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Apache Cassandra API para a base de dados do Azure Cosmos suportarão comandos de estado do nó adição/cluster/nó Estado?
Apache Cassandra API é um serviço de plataforma que torna o planeamento de capacidade, a responder às necessidades de débito & armazenamento elasticidade um breeze. Com base de dados do Azure Cosmos aprovisionar débito que precisa. Em seguida, pode dimensioná-lo para cima e baixo qualquer número de vezes através do dia sem se preocupar nós a adicionar/eliminar ou gerir. Isto implica que não tem de utilizar a ferramenta de gestão do cluster, do nó demasiado. 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece no que respeita à várias definições de configuração para a criação de keyspace como simples/rede?
BD do Azure do Cosmos fornece distribuição global Box para disponibilidade e as razões de latência baixa. Não é necessário para réplicas de configuração etc. Todas as escritas são sempre forma durável quórum consolidada numa qualquer região onde escreve ao fornecer garantias de desempenho.  

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-etc"></a>O que acontece no que respeita à várias definições para os metadados da tabela como filtro bloom, colocação em cache, leia a alteração de reparação, gc_grace, compressão memtable_flush_period etc?
BD do Azure do Cosmos proporciona um desempenho para leituras/escritas e débito sem necessidade de alterar qualquer uma das definições de configuração e de forma acidental de manipulá-los.  

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Time-to-live (TTL) é suportada em tabelas de Cassandra? 
Sim, o TTL é suportada. 

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>É possível monitorizar o estado do nó, o estado da réplica, o gc e o SO os parâmetros anteriormente com várias ferramentas? O que necessita ser monitorizada agora?
BD do Cosmos do Azure é um serviço de plataforma que o ajuda a aumentar a produtividade e não se preocupe a gerir e monitorizar a infraestrutura. Precisa de tratar dos processos débito que está disponível no portais métricas para determinar se a introdução limitada e aumenta ou diminuir que débito. Monitor [SLAs](monitor-accounts.md).
Utilize [métricas](use-metrics.md) utilize [registos de diagnóstico](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Os SDKs do cliente podem trabalhar com Apache Cassandra API do Cosmos BD do Azure?
Cliente do SDK de pré-visualização privada, o Apache Cassandra controladores que utilizam CQLv3 foram utilizadas para programas de cliente. Se tiver outros controladores que pode utilizar ou se estão a enfrentar problemas, envie um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="is-composite-primary-key-supported"></a>Chave primária composta é suportada?
Sim, pode utilizar a sintaxe regular para criar a chave de partição composto. 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>Pode utilizar sstable carregador para carregamento de dados?
Não, durante a pré-visualização sstable carregador não é suportada. 

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>Um cluster de cassandra no local pode ser emparelhado da BD do Azure Cosmos Apache Cassandra API?
No presente BD do Cosmos Azure tem uma experiência otimizada para o ambiente de nuvem sem a sobrecarga de operações. Se necessitar de emparelhamento, envie para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) com uma descrição do seu cenário.

### <a name="does-cassandra-api-provide-full-backups"></a>API de Cassandra fornece cópias de segurança completas? 
BD do Cosmos do Azure fornece duas livres cópias de segurança completas executadas num intervalo de quatro horas hoje em todos os APIs. Isto garante que não terá de configurar uma agenda de cópia de segurança etc. Se pretender modificar a retenção e a frequência, envie um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) ou emitir um incidente de suporte. São fornecidas informações sobre a capacidade de cópia de segurança no [automática cópia de segurança online e de restauro com base de dados do Azure Cosmos](online-backup-and-restore.md) artigo. 

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como a conta de Cassandra API processar ativação pós-falha se uma região ficar inativo? 
A API do Azure Cosmos DB Cassandra borrows da plataforma global distribuída da base de dados do Azure Cosmos. Para garantir que a aplicação pode tolerar o período de indisponibilidade do Centro de dados, ativar, pelo menos, uma região mais para a conta no portal do Azure Cosmos DB [desenvolver com contas de base de dados do Azure Cosmos multirregião](regional-failover.md). Pode definir a prioridade da região utilizando o portal [desenvolver com contas de base de dados do Azure Cosmos multirregião](regional-failover.md). 

Pode adicionar tantos regiões à medida que pretende para a conta e controla em que pode efetuar a ativação pós-falha, fornecendo uma prioridade de ativação pós-falha. Para utilizar a base de dados, tem de fornecer uma aplicação não existe demasiado. Se o fizer, os seus clientes não verificará o período de indisponibilidade. 

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>A API do Apache Cassandra índice todos os atributos de uma entidade por predefinição?
Sim, todos os atributos de uma entidade são indexados por predefinição pelo Azure Cosmos DB. Para obter mais informações, consulte [BD do Azure Cosmos: indexação políticas](indexing-policies.md). Obter benefícios de desempenho garantido com indexação consistente e quórum durável consolidada escreve sempre. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Esta média não é necessário criar vários índices para satisfazer as consultas? 
Sim, a base de dados do Azure Cosmos fornece a indexação automática de todos os atributos sem qualquer definição de esquema. Esta automatização liberta os programadores concentrar-se na aplicação, em vez de na gestão e criação de índices. Para obter mais informações, consulte [BD do Azure Cosmos: indexação políticas](indexing-policies.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso utilizar o SDK de API Cassandra novo localmente com o emulador?
Planeamos suportar esta capacidade no futuro. 

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-changefeed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>BD do Azure do Cosmos como uma plataforma parece ter muitos capacidades, como changefeed e outras funcionalidades. Serão adicionadas estas capacidades para a API de Cassandra? 
A API do Apache Cassandra fornece a mesma funcionalidade CQL como Apache Cassandra. Planeamos a viabilidade da diversas capacidades de suporte no futuro.

### <a name="feature-x-of-regular-cassandra-api-is-not-working-as-today-where-can-the-feedback-be-provided"></a>Funcionalidade x regular Cassandra API não está a funcionar como hoje, onde podem os comentários ser fornecidos?
Fornecer comentários através do [comentários do uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>Questões a partir de clientes do DocumentDB
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Por que razão são mover a base de dados do Azure Cosmos? 

BD do Cosmos do Azure é o seguinte bissexto grande nas bases de dados de nuvem global distribuída, à escala. Como um cliente do DocumentDB, tem agora acesso ao sistema breakthrough e capacidades oferecidas pelo Azure Cosmos DB.

Iniciar o BD do Azure do Cosmos como "Projeto Florence" no 2010 para abordar os pontos de tensão deparam pelos programadores na criação de aplicações em grande escala no interior da Microsoft. Os desafios de criação de aplicações distribuídas global não são exclusivos para a Microsoft, pelo que iremos disponibilizados a geração de primeiro esta tecnologia 2015 para programadores do Azure no formulário do Azure DocumentDB. 

Desde que essa hora, adicionar novas funcionalidades e introduzidos capacidades significativas de novo. BD do Azure do Cosmos é o resultado. Como parte desta versão, os clientes do DocumentDB, com os respetivos dados automaticamente e de forma totalmente integrada tornar-se os clientes de base de dados do Azure Cosmos. Estas capacidades são nas áreas do motor de base de dados principal, bem como distribuição global, escalabilidade elástica e SLAs líder da indústria, abrangentes. Mais concretamente, podemos evoluíram o motor de base de dados de base de dados do Azure Cosmos eficientemente mapear todos os modelos de dados mais populares, sistemas de tipo e APIs para o modelo de dados subjacente da BD do Cosmos do Azure. 

O manifestation orientado para o programador atual deste trabalho é o novo suporte para [Gremlin](../cosmos-db/graph-introduction.md) e [tabela APIs de armazenamento](../cosmos-db/table-introduction.md). E isto é apenas o início. Planeamos adicionar outras APIs populares e mais recentes modelos de dados ao longo do tempo, com mais avançada no desempenho e de armazenamento à escala global. 

É importante realçar que o DocumentDB [dialeto SQL](../documentdb/documentdb-sql-query.md) foi sempre apenas uma das muitas APIs que pode suportar a BD do Cosmos Azure subjacente. Para programadores que utilizam um serviço completamente gerido, tais como a base de dados do Azure Cosmos, a interface apenas para o serviço é as APIs que sejam expostas pelo serviço. Nada altera realmente para clientes do DocumentDB existentes. Na base de dados do Azure Cosmos, obter exatamente o mesmo API do SQL Server que o DocumentDB oferece. E agora (e no futuro), pode aceder a outras capacidades anteriormente inacessíveis 

Outro manifestation do nosso trabalho contínuo é o alicerce expandido da escalabilidade elástica e global do débito e armazenamento. Efetuamos melhoramentos dos vários para o subsistema de distribuição global. Uma das muitas essas orientado para o programador funcionalidades é o modelo de consistência do prefixo consistente, tornando um total cinco modelos de consistência bem definidos. Será Lançamos muitas funcionalidades mais interessantes como estes madura. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>O que é necessário para garantir que os meus recursos do DocumentDB continuam a ser executado na base de dados do Azure Cosmos?

Não precisa de efetuar todas as alterações. Os recursos do DocumentDB são agora recursos de base de dados do Azure Cosmos e foi sem interrupções no serviço quando ocorreu esta mudança.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>As alterações que é necessário fazer para a minha aplicação trabalhar com a base de dados do Azure Cosmos?

Não foram efetuadas alterações a efetuar. Nomes de pacote NuGet, classes e espaços de nomes não foram alteradas. Como sempre, recomendamos que mantenha os SDKs atualizados para tirar partido das funcionalidades mais recentes e melhoramentos. 

### <a name="whats-changed-in-the-azure-portal"></a>O que é alterado no portal do Azure?

O DocumentDB deixa de aparecer no portal como um serviço do Azure. No seu lugar é um novo ícone de BD do Cosmos do Azure, conforme mostrado na imagem seguinte. Todas as suas coleções estão disponíveis, à medida que se encontravam antes, ainda pode dimensionar débito, níveis de consistência de alteração e monitorizar SLAs. As capacidades do Explorador de dados (pré-visualização) foram melhoradas. Agora pode ver e editar documentos, criar e executar consultas e trabalhar com procedimentos armazenados, acionadores e UDF a partir de uma página, conforme mostrado na imagem seguinte: 

![A página de coleções de BD do Cosmos do Azure](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>Existem alterações preços?

Não, o custo de executar a sua aplicação no Azure Cosmos DB é o mesmo que estava antes.

### <a name="are-there-changes-to-the-slas"></a>Existem alterações para os SLAs?

Não, os SLAs para disponibilidade, consistência, débito e latência são iguais e ainda são apresentados no portal. Para obter mais informações, consulte [SLA para a base de dados do Azure Cosmos](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Aplicação de tarefas com dados de exemplo](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
