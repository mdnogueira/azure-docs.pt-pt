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
ms.date: 11/02/2017
ms.author: mimig
ms.openlocfilehash: 091446fd45b09913dee70dbb4c7e5ebbca02819b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-cosmos-db-faq"></a>FAQ do Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Noções básicas do Cosmos BD do Azure
### <a name="what-is-azure-cosmos-db"></a>O que é a base de dados do Azure Cosmos?
BD do Cosmos do Azure é um serviço de base de dados globalmente replicados e múltiplos modelo que oferece consultas avançadas sobre os dados sem esquema, ajuda a proporcionar um desempenho configurável e fiável e permite um desenvolvimento rápido. Se todos os alcançada através de uma plataforma gerida que é efetuada pela potência e alcance do Microsoft Azure. 

BD do Cosmos do Azure é a solução certa para a web, móveis, jogos, e aplicações IoT quando o débito previsível, elevada disponibilidade, latência baixa e um modelo de dados sem esquema são requisitos principais. Fornece flexibilidade de esquema e indexação avançada e inclui suporte de transacional com vários documento com JavaScript integrado. 

Para obter mais perguntas de base de dados, respostas e instruções para implementar e utilizar este serviço, consulte o [página de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>O que aconteceu ao DocumentDB?
A API do DocumentDB é um dos modelos de dados para a base de dados do Azure Cosmos e APIs suportadas. Além disso, base de dados do Azure Cosmos suporta, com Graph API (pré-visualização), a API de tabela (pré-visualização) e a API do MongoDB. Para obter mais informações, consulte [questões a partir de clientes do DocumentDB](#moving-to-cosmos-db).

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
Se precisar de qualquer ajuda, entrar-no [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) ou [fórum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB), ou agendar um chat cara com a equipa de engenharia da BD do Cosmos Azure através do envio de correio a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com). 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Tente subscrições do Azure Cosmos DB

Podem agora desfrutar uma experiência de base de dados do Azure Cosmos limitado de tempo sem uma subscrição, de forma gratuita e compromissos. Para se inscrever para uma subscrição de tente DB Cosmos do Azure, aceda a [tente BD do Azure do Cosmos gratuitamente](https://azure.microsoft.com/try/cosmosdb/). Esta subscrição está separada do [avaliação gratuita do Azure](https://azure.microsoft.com/free/)e pode ser utilizado para além de uma versão de avaliação gratuita do Azure ou um Azure subscrição paga. 

Tente Azure Cosmos DB subscrições são apresentados no portal do Azure junto outras subscrições associadas à sua ID de utilizador. 

As seguintes condições aplicam-se a BD do Cosmos de Azure tente subscrições:

* Um contentor por subscrição para o SQL Server (API do DocumentDB), Gremlin (Graph API) e contas de API de tabela.
* Até 3 coleções por subscrição para contas do MongoDB.
* Capacidade de armazenamento de 10 GB.
* Replicação global está disponível a seguir [regiões do Azure](https://azure.microsoft.com/regions/): EUA Central, Europa do Norte e Sudeste asiático
* Débito máximo de 5 mil RU/s.
* Subscrições expiram após 24 horas e podem ser expandidas até um máximo de total de 48 horas.
* Não não possível criar pedidos de suporte do Azure para contas de tente BD do Azure Cosmos; No entanto, é fornecido suporte para subscritores com planos de suporte existentes. 

## <a name="set-up-azure-cosmos-db"></a>Configurar a base de dados do Azure Cosmos
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como posso inscrever-me para a base de dados do Azure Cosmos?
BD do Cosmos do Azure está disponível no portal do Azure. Em primeiro lugar, inscreva-se uma subscrição do Azure. Depois de se ter inscrito, pode adicionar uma API do DocumentDB, Graph API (pré-visualização), API de tabela (pré-visualização) ou conta da API do MongoDB à sua subscrição do Azure.

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

## <a name="develop-with-the-table-api-preview"></a>Desenvolver com a tabela de API (pré-visualização)

### <a name="terms"></a>Termos 
A API de tabela do Cosmos DB no Azure (pré-visualização) refere-se para o premium oferta por base de dados do Azure Cosmos para um modelo de dados de tabela anunciado a compilação de 2017. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>Como posso utilizar a nova oferta de API de tabela (pré-visualização)? 
A API de tabela de base de dados do Azure Cosmos está disponível no [portal do Azure][azure-portal]. Primeiro, deve inscrever-se uma subscrição do Azure. Depois de se ter inscrito, pode adicionar uma conta de API de tabela de base de dados do Azure Cosmos à sua subscrição do Azure e, em seguida, adicione as tabelas para a sua conta. 

Durante o período de pré-visualização, quando [SDKs](../cosmos-db/table-sdk-dotnet.md) estão disponíveis para .NET, pode começar a utilizar, concluindo o [API de tabela](../cosmos-db/create-table-dotnet.md) artigo de início rápido.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>É necessário um novo SDK para utilizar a API de tabela (pré-visualização)? 
Sim, o [tabelas de Premium do armazenamento do Azure (pré-visualização) SDK](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable) está disponível no NuGet e é necessária para utilizar a API de tabela de base de dados do Azure Cosmos. Informações adicionais estão disponíveis no [tabela .NET API do Azure Cosmos DB: transferir e notas de versão](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md) página. 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como posso fornecer comentários sobre o SDK ou erros?
Pode partilhar os seus comentários em qualquer uma das seguintes formas:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [Fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>O que é a cadeia de ligação que tenho de utilizar para ligar à API de tabela (pré-visualização)?
A cadeia de ligação é:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com
```
Pode obter a cadeia de ligação da página de chaves no portal do Azure. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>Como posso substituir as definições de configuração para as opções de pedido na nova API de tabela (pré-visualização)?
Para informações sobre definições de configuração, consulte [capacidades de base de dados do Azure Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Pode alterar as definições adicionando-os à App. config na secção appSettings a aplicação de cliente.

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdk"></a>Existem alterações para os clientes que utilizam o armazenamento de tabelas do Azure SDK existente?
nenhum. Não foram efetuadas alterações para os clientes de novas ou existentes que estão a utilizar o armazenamento de tabelas do Azure existente SDKs. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Como posso ver dados de tabela que são armazenados na base de dados do Azure Cosmos para utilização com a API de tabela (consulte)? 
Pode utilizar o portal do Azure para procurar os dados. Também pode utilizar o código da API de tabela (pré-visualização) ou as ferramentas mencionadas na resposta seguinte. 

### <a name="which-tools-work-with-the-table-api-preview"></a>As ferramentas de trabalham com a API de tabela (pré-visualização)? 
Pode utilizar a versão mais antiga do Explorador do Azure (0.8.9).

Ferramentas com a flexibilidade de tirar uma cadeia de ligação no formato especificado anteriormente podem suportar a nova API de tabela (pré-visualização). É fornecida uma lista das ferramentas de tabela no [ferramentas de cliente de armazenamento do Azure](../storage/common/storage-explorers.md) página. 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>PowerShell ou a CLI do Azure funcionam com a nova API de tabela (pré-visualização)?
Planeamos adicionar suporte do PowerShell e da CLI do Azure para a API de tabela (pré-visualização). 

### <a name="is-the-concurrency-on-operations-controlled"></a>É a simultaneidade operações controlada?
Sim, a simultaneidade otimista é fornecida através da utilização de mecanismo a ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>O modelo de consulta de OData é suportado para entidades? 
Sim, a API de tabela (pré-visualização) suporta a consulta de OData e consultas LINQ. 

### <a name="can-i-connect-to-the-azure-table-storage-and-the-azure-cosmos-db-table-api-preview-side-by-side-in-the-same-application"></a>Pode ligar para o Table storage do Azure e a API do Azure Cosmos DB tabela (pré-visualização) lado na mesma aplicação? 
Sim, pode ligar ao criar duas instâncias separadas do CloudTableClient, cada apontar para o seu próprio URI através da cadeia de ligação.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>Como posso migrar uma aplicação de armazenamento de tabelas do Azure existente para esta nova oferta?
Para tirar partido da nova oferta de API de tabela de base de dados do Azure Cosmos nos seus dados de armazenamento de tabela existentes, contacte [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com). 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>O que é o plano para este serviço e, quando irá oferecer outras funcionalidades de API de tabela padrão?
Planeamos adicionar suporte para tokens SAS, ServiceContext, estatísticas, encriptação, análise e outras funcionalidades do lado do cliente à medida que avança para depois da disponibilidade geral Pode dar-nos comentários no [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Como é expansão de tamanho de armazenamento feito para este serviço se, por exemplo, iniciar com  *n*  GB de dados e os meus dados irá aumentar 1 TB ao longo do tempo? 
BD do Azure do Cosmos foi concebida para fornecer armazenamento ilimitado através da utilização de dimensionamento horizontal. O serviço pode monitorizar e aumentar eficazmente o seu armazenamento. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Como monitorizar a oferta de API de tabela (pré-visualização)?
Pode utilizar a API de tabela (pré-visualização) **métricas** painel no portal do Azure para monitorizar os pedidos e utilização do armazenamento. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Como calcular o débito que exigir?
Pode utilizar o estimator de capacidade para calcular o TableThroughput necessárias para as operações. Para obter mais informações, consulte [armazenamento de dados e unidades de pedido de estimativa](https://www.documentdb.com/capacityplanner). Em geral, pode representar entidade como JSON e fornecer os números para as operações. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>Pode utilizar a nova API de tabela (pré-visualização) SDK localmente com o emulador?
Sim, pode utilizar a API de tabela (pré-visualização) com o emulador local ao utilizar o SDK de novo. Para transferir o novo emulador, visite [utilizar o emulador de BD do Cosmos do Azure para desenvolvimento local e o teste](local-emulator.md). O valor de StorageConnectionString na App. config tem de ser:

```
DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`. 
```

### <a name="can-my-existing-azure-table-storage-application-work-with-the-table-api-preview"></a>A minha aplicação de armazenamento de tabelas do Azure existente pode trabalhar com a API de tabela (pré-visualização)? 
A área de superfície da API de tabela nova (pré-visualização) é compatível com o armazenamento de tabelas do Azure SDK existente em toda a criar, eliminar, atualização e operações de consulta na .NET API. Certifique-se de que tem uma chave de linha, porque a API de tabela (pré-visualização) requer uma chave de partição e uma chave de linha. Também iremos planear adicionar mais suporte do SDK, à medida que avança para GA desta oferta de serviço.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>É necessário migrar as minhas aplicações de armazenamento de tabelas do Azure existentes para o novo SDK se não pretender utilizar as funcionalidades de API de tabela (pré-visualização)?
Não, pode criar e utilizar ativos de armazenamento de tabelas do Azure existentes sem interrupção de qualquer tipo. No entanto, se utilizar a nova API de tabela (pré-visualização), não podem beneficiar do índice automático, a opção de consistência adicionais ou distribuição global. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-preview-across-multiple-regions-of-azure"></a>Como adicionar replicação dos dados na tabela de API (pré-visualização) em várias regiões do Azure?
Pode utilizar o portal do Azure Cosmos DB [as definições de replicação global](tutorial-global-distribution-documentdb.md#portal) adicionar regiões que são adequadas para a sua aplicação. Para desenvolver uma aplicação distribuída global, deve também adicionar a sua aplicação com as informações de PreferredLocation definidas para a região local para fornecer a leitura de latência baixa. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api-preview"></a>Como posso alterar a região primária escrita para a conta na tabela de API (pré-visualização)?
Pode utilizar o painel de portal de replicação global de BD do Cosmos do Azure para adicionar uma região e, em seguida, efetuar a ativação pós-falha para a região necessária. Para obter instruções, consulte [desenvolver com contas de base de dados do Azure Cosmos multirregião](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como configurar o meu regiões leitura preferenciais de latência baixa quando posso distribuir os meus dados? 
Para ajudar a ler a partir da localização local, utilize a chave de PreferredLocation no ficheiro App. config. Para as aplicações existentes, a API de tabela (pré-visualização) emitir um erro se LocationMode está definido. Remova esse código, porque o premium API de tabela (pré-visualização) escolherá estas informações a partir do ficheiro App. config. Para obter mais informações, consulte [capacidades de base de dados do Azure Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>Como necessário pensar sobre os níveis de consistência na tabela de API (pré-visualização)? 
BD do Azure do Cosmos fornece bem reasoned compromissos entre consistência, disponibilidade e a latência. BD do Azure do Cosmos oferece cinco níveis de consistência para os programadores de API de tabela (pré-visualização), para que possa escolher o modelo de consistência direito ao nível da tabela e fazer pedidos individuais ao consultar os dados. Quando um cliente se liga, pode especificar um nível de consistência. Pode alterar o nível através da definição de App. config para o valor da chave TableConsistencyLevel. 

A API de tabela (pré-visualização) fornece a latência baixa lê com "ler as seus próprios escritas," com consistência de sessão como predefinição. Para obter mais informações, consulte [níveis de consistência](consistency-levels.md). 

Por predefinição, o Table storage do Azure fornece a consistência forte numa região e consistência Eventual nas localizações secundárias. 

### <a name="does-the-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>A API de tabela de base de dados do Azure Cosmos oferecem mais níveis de consistência ao Table storage do Azure?
Sim, para obter informações sobre como beneficiar a natureza distribuída do Azure Cosmos DB, consulte o artigo [níveis de consistência](consistency-levels.md). Porque garantias são fornecidas para os níveis de consistência, pode utilizá-los com confiança. Para obter mais informações, consulte [capacidades de base de dados do Azure Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global está ativada, quanto tempo demora a replicar os dados?
BD do Azure do Cosmos consolida os dados de forma durável na região local e envia os dados para outras regiões imediatamente num fim de milissegundos. Esta replicação está dependente apenas na hora reportadas round-trip (RTT) do Centro de dados. Para saber mais sobre a capacidade de distribuição global do Azure Cosmos DB, consulte o artigo [BD do Cosmos do Azure: um serviço de base de dados globalmente distribuída no Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>É possível alterar o nível de consistência de pedido de leitura?
Com base de dados do Azure Cosmos, pode definir o nível de consistência ao nível do contentor (na tabela). Ao utilizar o SDK, pode alterar o nível ao fornecer o valor para a chave de TableConsistencyLevel no ficheiro App. config. Os valores possíveis são: forte, consistência vinculada, sessão, prefixo consistente e Eventual. Para obter mais informações, consulte [níveis de consistência Sincronizáveis dados na base de dados do Azure Cosmos](consistency-levels.md). A chave ideia é que não é possível definir a consistência de pedido nível em mais do que a definição da tabela. Por exemplo, não é possível definir o nível de consistência para a tabela Eventual e o nível de consistência do pedido em forte. 

### <a name="how-does-the-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>Como a conta de API de tabela (pré-visualização) processar ativação pós-falha se uma região ficar inativo? 
A API de tabela do Cosmos DB no Azure (pré-visualização) borrows da plataforma global distribuída da base de dados do Azure Cosmos. Para garantir que a aplicação pode tolerar o período de indisponibilidade do Centro de dados, ativar, pelo menos, uma região mais para a conta no portal do Azure Cosmos DB [desenvolver com contas de base de dados do Azure Cosmos multirregião](regional-failover.md). Pode definir a prioridade da região utilizando o portal [desenvolver com contas de base de dados do Azure Cosmos multirregião](regional-failover.md). 

Pode adicionar tantos regiões à medida que pretende para a conta e controla em que pode efetuar a ativação pós-falha, fornecendo uma prioridade de ativação pós-falha. Obviamente, para utilizar a base de dados, tem de fornecer uma aplicação não existe demasiado. Se o fizer, os seus clientes não verificará o período de indisponibilidade. O cliente SDK é auto homing. Ou seja, que pode detetar a região que está inativo e automaticamente a ativação pós-falha para a região de novo.

### <a name="is-the-table-api-preview-enabled-for-backups"></a>A API de tabela (pré-visualização) está ativada para cópias de segurança?
Sim, a API de tabela do Cosmos DB no Azure (pré-visualização) borrows da plataforma de base de dados do Azure Cosmos para cópias de segurança. As cópias de segurança são efetuadas automaticamente. Para obter mais informações, consulte [cópia de segurança Online e de restauro com base de dados do Azure Cosmos](online-backup-and-restore.md).

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>A API de tabela (pré-visualização) índice todos os atributos de uma entidade por predefinição?
Sim, todos os atributos de uma entidade são indexados por predefinição pelo Azure Cosmos DB. Para obter mais informações, consulte [BD do Azure Cosmos: indexação políticas](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Esta média não é necessário criar vários índices para satisfazer as consultas? 
Sim, a base de dados do Azure Cosmos fornece a indexação automática de todos os atributos sem qualquer definição de esquema. Esta automatização liberta os programadores concentrar-se na aplicação, em vez de na gestão e criação de índices. Para obter mais informações, consulte [BD do Azure Cosmos: indexação políticas](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Pode alterar a política de indexação?
Sim, pode alterar a política de indexação fornecendo a definição do índice. Para obter mais informações, consulte [capacidades de base de dados do Azure Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Terá de corretamente codificar e as definições de escape. 

No formato de cadeia json no ficheiro App. config:
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
Pré-visualização, a API de tabela fornece a mesma funcionalidade de consulta que o Table storage do Azure. BD do Azure do Cosmos também suporta a ordenação, os agregados, geoespacial consulta, hierarquia e uma vasta gama de funções incorporadas. Podemos irá fornecer funcionalidades adicionais na API tabela numa atualização futura do serviço. Para obter mais informações, consulte [as consultas SQL para a API de DocumentDB do Azure Cosmos DB](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Quando posso alterar TableThroughput para a API de tabela (pré-visualização)?
Deve alterar TableThroughput quando qualquer uma das seguintes condições aplicam-se:
* Se estiver a efetuar uma extração, transformação e carregamento (ETL) de dados ou que pretende carregar uma grande quantidade de dados num curto período de tempo. 
* Precisa de mais débito do contentor no back-end. Por exemplo, verá que o débito utilizado mais do que o débito aprovisionado e pode obter limitadas. Para obter mais informações, consulte [débito de conjunto para contentores de base de dados do Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>Pode aumentar verticalmente ou reduzir verticalmente o débito da minha tabela de API de tabela (pré-visualização)? 
Sim, pode utilizar o painel de escala do portal do Azure Cosmos DB para aumentar o débito. Para obter mais informações, consulte [conjunto débito](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>É uma predefinição que tablethroughput definida para tabelas recentemente aprovisionadas?
Sim, se não substituir TableThroughput através de App. config e não utilizar um contentor previamente criado na base de dados do Azure Cosmos, o serviço cria uma tabela com débito de 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-azure-table-storage"></a>É efetuada qualquer alteração de preço para clientes existentes do Table storage do Azure?
nenhum. Não há nenhuma alteração no preço para clientes de armazenamento de tabelas do Azure existentes. 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>Como é o preço calculado para a API de tabela (pré-visualização)? 
Os preços da API de tabela de base de dados de Cosmos do Azure (pré-visualização) dependem o TableThroughput alocado. 

### <a name="how-do-i-handle-any-throttling-in-table-api-preview-offering"></a>Como processar qualquer limitação oferta de API de tabela (pré-visualização)? 
Se a velocidade do pedido excede a capacidade de débito aprovisionado para o contentor subjacente, obterá um erro e o SDK tentará a chamada por aplicar a política de repetição.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-azure-cosmos-db-table-api-preview"></a>Por que motivo é necessário escolher um débito para além dos PartitionKey e RowKey para tirar partido da API de tabela de base de dados de Cosmos do Azure (pré-visualização)?
BD do Azure do Cosmos define um débito predefinido para o contentor se não fornecer uma no ficheiro App. config. 

BD do Azure do Cosmos fornece garantias de desempenho e a latência, com os limites superior a operação. Este garantia é possível quando o motor pode impor governação nas operações do inquilino. Definição TableThroughput garante que obtém o garantida débito e latência, porque a plataforma de reserva esta capacidade e garantias de êxito operacional. 

Ao utilizar a especificação de débito, aprovisionadas que pode alterá-lo para beneficiar da sazonalidade da sua aplicação, para satisfazer as necessidades de débito e reduzir os custos.

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>SDK de armazenamento do Azure foi muito económico para me permitir, porque posso pagar apenas para armazenar os dados e posso raramente consulta. A nova oferta de base de dados do Azure Cosmos parece ser charging-me, apesar de não efetuar uma única transação ou armazenados nada. Pode,. explicam?

BD do Azure do Cosmos foi concebido para ser um sistema global distribuído, com base no SLA com garantias de disponibilidade, débito e latência. Quando reservar débito do BD Azure Cosmos, é garantido, ao contrário do débito de outros sistemas. BD do Cosmos do Azure fornece funcionalidades adicionais que os clientes pedidas, como índices secundários e distribuição global.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nunca é apresentada uma notificação de "quota completo" (indica que uma partição é completa) quando posso ingerir dados no Table storage do Azure. Com a API de tabela (pré-visualização), é apresentada esta mensagem. É esta a oferta limitando-me e forçar-me para alterar a minha aplicação existente?

BD do Cosmos do Azure é um sistema com base no SLA que fornece o dimensionamento ilimitado, com garantias de latência, débito, disponibilidade e consistência. Para garantir o desempenho premium garantido, certifique-se de que o tamanho dos dados e índice são geríveis e dimensionável. O limite de 10 GB no número de entidades ou os itens por chave de partição é garantir que fornecemos um excelente desempenho de consulta e de pesquisa. Para garantir que a aplicação dimensiona bem mesmo para o Storage do Azure, recomendamos que lhe *não* criar uma partição quente ao armazenar todas as informações na partição de um e -lo a consultar. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>Por isso, PartitionKey RowKey é ainda necessário e com a nova API de tabela (pré-visualização)? 
Sim. Como a área de superfície da API de tabela (pré-visualização) é semelhante à que o Table storage do Azure SDK, a chave de partição fornece uma forma eficaz para distribuir os dados. A chave de linha é exclusiva dentro dessa partição. A chave da fila tem de estar presente e não pode ser nula como o SDK padrão. O comprimento do RowKey é 255 bytes e o comprimento do PartitionKey é 100 bytes (em breve para ser aumentado para 1 KB). 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>Quais são as mensagens de erro para a API de tabela (pré-visualização)?
Porque esta pré-visualização é compatível com o Table storage do Azure, a maioria dos erros irá mapear os erros da tabela padrão. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>Por que motivo posso obter limitado ao tentar criar após outro muita das tabelas na tabela de API (pré-visualização)?
BD do Cosmos do Azure é um sistema com base no SLA que fornece a latência, débito, disponibilidade e garantias de consistência. Porque é um sistema aprovisionado, reserva recursos para garantir estes requisitos. A taxa rápida de criação de tabelas é detetada e limitada. Recomendamos que observe a taxa de criação de tabelas e reduzi-lo para menos de 5 por minuto. Lembre-se de que a API de tabela (pré-visualização) é um sistema aprovisionado. Neste momento que aprovisionar, começará pagar para o mesmo. 

## <a name="develop-against-the-graph-api-preview"></a>Desenvolver contra a Graph API (pré-visualização)
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Como pode aplicar a funcionalidade da Graph API (pré-visualização) à base de dados do Azure Cosmos?
Pode utilizar uma biblioteca de extensão para aplicar a funcionalidade da Graph API (pré-visualização). Esta biblioteca é chamada gráficos do Microsoft Azure e está disponível no NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Parece que suporta o idioma de transversal Gremlin gráfico. Planear adicionar mais formas de consulta?
Sim, planeamos adicionar outros mecanismos para a consulta no futuro. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>Como posso utilizar a nova oferta de Graph API (pré-visualização)? 
Para começar a utilizar, execute o [Graph API](../cosmos-db/create-graph-dotnet.md) artigo de início rápido.

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>Questões a partir de clientes do DocumentDB
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Por que razão são mover a base de dados do Azure Cosmos? 

BD do Cosmos do Azure é o seguinte bissexto grande nas bases de dados de nuvem global distribuída, à escala. Como um cliente do DocumentDB, tem agora acesso ao sistema breakthrough e capacidades oferecidas pelo Azure Cosmos DB.

Iniciar o BD do Azure do Cosmos como "Projeto Florence" no 2010 para abordar os pontos de tensão deparam pelos programadores na criação de aplicações em grande escala no interior da Microsoft. Os desafios de criação de aplicações distribuídas global não são exclusivos para a Microsoft, pelo que iremos disponibilizados a geração de primeiro esta tecnologia 2015 para programadores do Azure no formulário do Azure DocumentDB. 

Desde que essa hora, adicionar novas funcionalidades e introduzidos capacidades significativas de novo. BD do Azure do Cosmos é o resultado. Como parte desta versão, os clientes do DocumentDB, com os respetivos dados automaticamente e de forma totalmente integrada tornar-se os clientes de base de dados do Azure Cosmos. Estas capacidades são nas áreas do motor de base de dados principal, bem como distribuição global, escalabilidade elástica e SLAs líder da indústria, abrangentes. Mais concretamente, podemos evoluíram o motor de base de dados de base de dados do Azure Cosmos eficientemente mapear todos os modelos de dados mais populares, sistemas de tipo e APIs para o modelo de dados subjacente da BD do Cosmos do Azure. 

O manifestation orientado para o programador atual deste trabalho é o novo suporte para [Gremlin](../cosmos-db/graph-introduction.md) e [APIs de tabela](../cosmos-db/table-introduction.md). E isto é apenas o início. Planeamos adicionar outras APIs populares e mais recentes modelos de dados ao longo do tempo, com mais avançada no desempenho e de armazenamento à escala global. 

É importante realçar que o DocumentDB [dialeto SQL](../documentdb/documentdb-sql-query.md) foi sempre apenas uma das muitas APIs que pode suportar a BD do Cosmos Azure subjacente. Para programadores que utilizam um serviço completamente gerido, tais como a base de dados do Azure Cosmos, a interface apenas para o serviço é as APIs que sejam expostas pelo serviço. Nada altera realmente para clientes do DocumentDB existentes. Na base de dados do Azure Cosmos, obter exatamente o mesmo API do SQL Server que o DocumentDB oferece. E agora (e no futuro), pode aceder a outras capacidades anteriormente inacessíveis 

Outro manifestation do nosso trabalho contínuo é o alicerce expandido da escalabilidade elástica e global do débito e armazenamento. Efetuamos melhoramentos dos vários para o subsistema de distribuição global. Uma das muitas essas orientado para o programador funcionalidades é o modelo de consistência do prefixo consistente, tornando um total cinco modelos de consistência bem definidos. Será Lançamos muitas funcionalidades mais interessantes como estes madura. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>O que é necessário para garantir que os meus recursos do DocumentDB continuam a ser executado na base de dados do Azure Cosmos?

É necessário não efetuar alterações de todo. Os recursos do DocumentDB são agora recursos de base de dados do Azure Cosmos e foi sem interrupções no serviço quando ocorreu esta mudança.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>As alterações que é necessário fazer para a minha aplicação trabalhar com a base de dados do Azure Cosmos?

Não foram efetuadas alterações a efetuar. Nomes de pacote NuGet, classes e espaços de nomes não foram alteradas. Como sempre, recomendamos que mantenha os SDKs atualizados para tirar partido das funcionalidades mais recentes e melhoramentos. 

### <a name="whats-changed-in-the-azure-portal"></a>O que é alterado no portal do Azure?

O DocumentDB deixa de aparecer no portal como um serviço do Azure. No seu lugar é um novo ícone de BD do Cosmos do Azure, conforme mostrado na imagem seguinte. Todas as suas coleções estão disponíveis, à medida que se encontravam antes, ainda pode dimensionar débito, níveis de consistência de alteração e monitorizar SLAs. As capacidades do Explorador de dados (pré-visualização) foram melhoradas. Agora pode ver e editar documentos, criar e executar consultas e trabalhar com procedimentos armazenados, acionadores e UDF a partir de uma página, conforme mostrado na imagem seguinte: 

![O painel de coleções de BD do Cosmos do Azure](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>Existem alterações preços?

Não, o custo de executar a sua aplicação no Azure Cosmos DB é o mesmo que estava antes.

### <a name="are-there-changes-to-the-slas"></a>Existem alterações para os SLAs?

Não, os SLAs para disponibilidade, consistência, débito e latência são iguais e ainda são apresentados no portal. Para obter mais informações, consulte [SLA para a base de dados do Azure Cosmos](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Aplicação de tarefas com dados de exemplo](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
