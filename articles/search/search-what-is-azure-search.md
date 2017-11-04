---
title: Novidades da Azure Search | Microsoft Docs
description: "A pesquisa do Azure é um serviço de pesquisa de nuvem alojado completamente gerido. Saiba mais nesta descrição geral da funcionalidade."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/26/2017
ms.author: ashmaka
ms.openlocfilehash: 9893be47ec0c2f58ca206ec7c1bce13734513390
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="what-is-azure-search"></a>O que é o Azure Search?
A pesquisa do Azure é uma solução de nuvem de pesquisa-como-um-serviço que fornece aos programadores APIs e ferramentas para adicionar uma experiência de pesquisa avançadas sobre os dados em aplicações web, móveis e enterprise.

A funcionalidade está exposta através de uma simples [REST API](/rest/api/searchservice/) ou [.NET SDK](search-howto-dotnet-sdk.md) que dissimula a complexidade inerente tecnologia de pesquisa. Para além das APIs, o portal do Azure fornece e suporte de fazer o protótipo da administração. Infraestrutura e disponibilidade são geridos pela Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Resumo da funcionalidade

| Categoria | Funcionalidades |
|----------|----------|
|Pesquisa em texto completo e análise de texto | [Pesquisa em texto completo](search-lucene-query-architecture.md) é o caso de utilização de um site primário para a maioria das aplicações baseada na procura. As consultas podem formulated utilizando uma sintaxe suportada. <br/><br/>[**Sintaxe de consulta simples** ](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) fornece operadores lógicos, operadores de pesquisa de expressão, operadores de sufixo, operadores de precedência.<br/><br/>[**Sintaxe de consulta Lucene** ](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) inclui todas as operações na sintaxe simple, com as extensões para pesquisa difusa, pesquisa de proximidade, os aumentos termo e expressões regulares.| 
| Integração de dados | Os índices de pesquisa do Azure aceitam dados a partir de qualquer origem fornecida é submetida como uma estrutura de dados JSON. <br/><br/> Opcionalmente, para origens de dados suportadas no Azure, pode utilizar [ **indexadores** ](search-indexer-overview.md) para automaticamente pesquisam [SQL Database do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-documentdb.md), ou [Blob storage do Azure](search-howto-indexing-azure-blob-storage.md) sincronizar o seu índice de pesquisa do conteúdo com o arquivo de dados principal. Indexadores de Blob do Azure podem efetuar *cracking documento* para [indexação formatos de ficheiro principais](search-howto-indexing-azure-blob-storage.md), incluindo o Microsoft Office, HTML e PDF, documentos. |
| Análise linguístico | Analisadores são componentes utilizados para processar durante as operações de pesquisa e indexação de texto. Existem dois tipos. <br/><br/>[**Analisadores de lexical personalizados** ](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) são utilizadas para consultas de pesquisa complexas utilizando a correspondência de fonético e expressões regulares. <br/><br/>[**Analisadores de idioma** ](https://docs.microsoft.com/rest/api/searchservice/language-support) Lucene ou Microsoft são utilizados para processar inteligentemente linguistics específicas de idiomas, incluindo tenses verbo, sexo, substantivos plural dados (por exemplo, ' rato' versus 'ratos'), word anular compounding, quebra de palavra (para idiomas sem espaços) e muito mais. |
| Georreplicação pesquisa | A pesquisa do Azure processa filtros e apresenta a localizações geográficas. Permite que os utilizadores explorar dados com base na proximidade de um resultado de pesquisa para uma localização física. [Veja este vídeo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) ou [rever este exemplo](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para obter mais informações. |
| Funcionalidades de experiência de utilizador | [**Sugestões de pesquisa** ](https://docs.microsoft.com/rest/api/searchservice/suggesters) pode ser ativada para consultas antecipada uma barra de pesquisa. Documentos reais no seu índice são sugeridos como os utilizadores introduzem uma pesquisa parcial de entrada. <br/><br/>[**Navegação por facetas** ](https://docs.microsoft.com/azure/search/search-faceted-navigation) é ativado através de um parâmetro de consulta simples. A pesquisa do Azure devolve uma estrutura de navegação por facetas que pode utilizar como o código atrás de uma lista de categorias para filtragem auto-direcionada (por exemplo, para filtrar os itens do catálogo por intervalo de preços ou marca). <br/><br/> [**Filtros** ](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) pode ser utilizado para incorporar navegação por facetas a IU da aplicação, melhorar formulação de consulta e filtrar com base em critérios especificado de um utilizador ou de programador. Crie filtros utilizando a sintaxe de OData.<br/><br/> [**Detetor de ocorrências** ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aplica-se para uma palavra-chave correspondente nos resultados da pesquisa de formatação de texto. Pode escolher os campos devolvem fragmentos realçados.<br/><br/>[**A ordenação** ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) é disponibilizado para vários campos através o esquema de índice e, em seguida, tivada/desativada durante a consulta com um parâmetro de procura única.<br/><br/> [**Paginação** ](search-pagination-page-layout.md) e limitação os resultados da pesquisa é simples com o controlo de lhe atento da Azure Search oferece sobre os resultados da pesquisa.  
| Relevância | [**Classificação simples** ](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) é uma vantagem de chave de pesquisa do Azure. Perfis de classificação são utilizados para modelar relevância como uma função de valores nos próprios documentos. Por exemplo, poderá querer produtos mais recentes ou subvalorizado produtos apareça superior nos resultados da pesquisa. Também pode criar perfis de classificação, utilizando etiquetas para classificação personalizada, com base nas preferências de pesquisa de cliente tiver controlada e armazenados em separado. |
| Monitorização e relatórios | [**Análise de tráfego de pesquisa** ](search-traffic-analytics.md) são recolhido e analisado para desbloquear os conhecimentos aprofundados sobre o que os utilizadores são escrevendo na caixa de pesquisa. <br/><br/>As métricas sobre consultas por segundo, latência e limitação são capturadas e reportadas nas páginas do portal, sem qualquer configuração adicional necessária. Também pode facilmente o índice de monitor e contagem de documentos para que pode ajustar capacidade conforme necessário. Para obter mais informações, consulte [administração do serviço](search-manage.md) |
| Ferramentas para fazer o protótipo e inspeção | No portal, pode utilizar o [ **Assistente para importar dados** ](search-import-data-portal.md) configurar indexadores, estruturador de índice para a segurança de um índice de estar posicionados e [ **Explorador de pesquisa** ](search-explorer.md)para testar as consultas e refinar os perfis de classificação. Também pode abrir um índice para ver o respetivo esquema. |
| Infraestrutura | O **plataforma elevada** garante uma experiência de serviço de pesquisa extremamente fiável. Quando ampliada corretamente, [da Azure Search oferece um SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Completamente geridos e dimensionáveis** como uma solução ponto-a-ponto, a Azure Search não necessita absolutamente nenhuma gestão de infraestrutura. O serviço pode ser personalizado para as suas necessidades por dimensionamento em duas dimensões para processar mais armazenamento de documentos, superior cargas de consulta ou ambos.

## <a name="how-to-use-azure-search"></a>Como utilizar a pesquisa do Azure
### <a name="step-1-provision-service"></a>Passo 1: Serviço de aprovisionamento
Pode rotação um serviço da Azure Search no [portal do Azure](https://portal.azure.com/) ou através de [API de gestão de recursos do Azure](/rest/api/searchmanagement/). Pode escolher qualquer o serviço gratuito partilhado com outros subscritores, ou um [paga camada](https://azure.microsoft.com/pricing/details/search/) que dedicates recursos utilizados apenas pelo seu serviço. Para os escalões pagos, pode dimensionar um serviço em duas dimensões: 

- Adicione as réplicas para aumentar a capacidade para processar cargas pesadas de consulta.   
- Adicione as partições para aumentar o armazenamento para obter mais documentos. 

Ao processar o débito de armazenamento e a consulta do documento em separado, pode calibrar os resourcing com base nos requisitos de produção.

### <a name="step-2-create-index"></a>Passo 2: Criar o índice
Antes de pode carregar conteúdo pesquisável, primeiro tem de definir um índice da Azure Search. É um índice como uma tabela de base de dados que contém os dados e pode aceitar a consultas de pesquisa. Definir o esquema de índice para mapear para refletir a estrutura dos documentos que pretende procurar, semelhante aos campos numa base de dados.

Um esquema pode ser criada no portal do Azure ou através de programação utilizando o [.NET SDK](search-howto-dotnet-sdk.md) ou [REST API](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>Passo 3: Dados de índice
Depois de definir um índice, está pronto para carregar conteúdo. Pode utilizar um modelo push ou pull.

O modelo de extração obtém dados a partir de origens de dados externas. É suportada através de *indexadores* que simplificar e automatizar aspetos da ingestão de dados, tais como ligar a, ler e serializar dados. [Indexadores](/rest/api/searchservice/Indexer-operations) estão disponíveis para a BD do Cosmos do Azure, SQL Database do Azure, Blob Storage do Azure e SQL Server alojado numa VM do Azure. Pode configurar um indexador para a pedido ou atualização de dados agendada.

O modelo de push é fornecido através do SDK ou APIs REST, utilizadas para enviar documentos atualizados para um índice. Pode enviar dados a partir de praticamente qualquer conjunto de dados utilizando o formato JSON. Consulte [adicionar, atualizar ou eliminar documentos](/rest/api/searchservice/addupdate-or-delete-documents) ou [como utilizar o SDK .NET)](search-howto-dotnet-sdk.md) para obter orientações sobre a carregar os dados.

### <a name="step-4-search"></a>Passo 4: pesquisa
Depois de preencher um índice, pode [emitir consultas de pesquisa](/rest/api/searchservice/Search-Documents) para o ponto final de serviço com pedidos de HTTP simples da REST API ou o SDK .NET.

## <a name="how-azure-search-compares"></a>Como compara a pesquisa do Azure

Os clientes peça, muitas vezes, como a pesquisa do Azure compara com outras soluções relacionadas com a pesquisa. A tabela seguinte resume as principais diferenças.

| Em comparação com | Principais diferenças |
|--|--|
|Bing | [API de pesquisa do Bing Web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) procura os índices em Bing.com termos correspondentes submeter. Os índices são criados a partir de HTML, XML e outros conteúdos de web sites público. [Pesquisa do Bing personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia de crawler para web tipos de conteúdo, no âmbito de web sites individuais.<br/><br/>A pesquisa do Azure procura um índice que definir, preenchida com dados e os documentos que possui, muitas vezes, de diversas fontes. A pesquisa do Azure tem crawler capabilies para algumas origens de dados através de [indexadores](search-indexer-overview.md), mas pode enviar quaisquer documentos JSON que está em conformidade com o esquema de índice para um recurso pesquisável único e consolidado. |
|Pesquisa de base de dados | [Pesquisa em texto completo do SQL Server](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) é para conteúdo interno para o DBMS, nas tabelas SQL. <br/><br/>A pesquisa do Azure armazena conteúdo a partir de origens heterogéneos e oferece texto especializado processamento funcionalidades, tais como análise linguístico e personalizada. O [motor de pesquisa de texto completo](search-lucene-query-architecture.md) na Azure Search está incorporado no Apache Lucene, uma norma na obtenção de informações da indústria. <br/><br/>Utilização de recursos é outro ponto de inflection. Pesquisa de linguagem natural é frequentemente viáveis intensiva. Descarregar procura uma solução dedicado preserva recursos para o processamento de transações. Pela pesquisa externalizing, pode ajustar facilmente escala para fazer corresponder o volume de consulta.|
|Solução de pesquisa dedicado | No local ou na nuvem soluções de serviço são pesquisa dedicado soluções com a funcionalidade do espetro completa. Tecnologias de pesquisa, normalmente, oferecem controlo sobre pipelines de indexação e consulta, de acesso para consultar mais rico e filtragem de sintaxe, controlam sobre a classificação e relevância e funcionalidades para pesquisa auto-direcionada e inteligente. <br/><br/>Pode encontrar soluções de pesquisa dedicado oferecidas como uma nuvem de serviço ou como um servidor autónomo alojado no local ou numa máquina virtual. Um serviço em nuvem é a escolha certa se pretender que um [ative chave solução com overhead mínimo e a manutenção e a escala ajustável](#cloud-service-advantage). <br/><br/>Dentro paradigma na nuvem, vários fornecedores de oferecem funcionalidades de linha de base comparável, com pesquisa de texto completo, georreplicação pesquisa e a capacidade de processar um determinado nível de ambiguidade em entradas de pesquisa. Normalmente, tem um [funcionalidade especializada](#feature-drilldown), ou de facilidade e simplicidade geral de APIs, ferramentas e gestão que determina a melhor opção. |

Entre os fornecedores de nuvem, é mais fortes para cargas de trabalho de pesquisa de texto completo da Azure Search através de arquivos de conteúdo e as bases de dados no Azure, para aplicações que dependem principalmente a pesquisa para obtenção de informações e conteúda navegação. Força da codificação chave incluem:

+ Integração de dados do Azure (crawlers) na camada de indexação
+ Portal do Azure para a gestão centralizada
+ Escala do Azure, fiabilidade e disponibilidade de trabalho
+ Análise de linguístico e personalizada, com analisadores de pesquisa em texto completo sólida 56 idiomas
+ [Principais funcionalidades de aplicações comuns a pesquisa centrada](#feature-drilldown): classificação, facetamento, sugestões, sinónimos, pesquisa georreplicação e muito mais.

> [!Note]
> Origens de dados do Azure não são totalmente suportadas, mas dependem de uma metodologia de push de mais de código intensivas em vez de indexadores. Utilizar APIs, pode encaminhar qualquer coleção de documentos JSON para um índice da Azure Search.

Entre os nossos clientes, os que são capazes de tirar partido do intervalo ser das funcionalidades na Azure Search incluem catálogos online, programas de linha de negócio e aplicações de deteção do documento.

## <a name="rest-api--net-sdk"></a>REST API | .net SDK

Muitas tarefas podem ser executada no portal, da Azure Search destina-se de que os programadores que pretendam integrar a funcionalidade de pesquisa para as aplicações existentes. As interfaces de programação seguintes estão disponíveis.

|Plataforma |Descrição |
|-----|------------|
|[REST](/rest/api/searchservice/) | Comandos HTTP suportados por qualquer idioma, incluindo Xamarin, Java e JavaScript e plataforma de programação|
|[SDK do .NET](search-howto-dotnet-sdk.md) | Dispositivo de moldagem do .NET para a API REST oferece codificação eficiente em c# e outras linguagens de código gerido direcionada para o .NET Framework |

## <a name="free-trial"></a>Avaliação gratuita
Os subscritores do Azure podem [aprovisionar um serviço no escalão gratuito](search-create-service-portal.md).

Se não são um subscritor, pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Obter créditos para experimentar serviços pagos do Azure. Depois de que está a utilizar, pode manter a conta e utilizar [serviços gratuitos do Azure](https://azure.microsoft.com/free/). Nunca é cobrado o seu cartão de crédito, exceto se alterar as definições e peça para ser cobrado explicitamente.

Em alternativa, pode [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar para serviços pagos do Azure. 

## <a name="how-to-get-started"></a>Como começar

1. Criar um serviço no [escalão gratuito](search-create-service-portal.md).

2. Siga os passos de uma ou mais dos seguintes tutoriais. 

  + [Como utilizar o SDK .NET](search-howto-dotnet-sdk.md) demonstra os principais passos no código gerido.  
  + [Introdução à REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) mostra os mesmos passos utilizando a API REST.  
  + [Criar o seu primeiro índice no portal do](search-get-started-portal.md) utilizar funcionalidades de indexação e protótipo incorporadas.   

Os motores de busca são os controladores de obtenção de informações em aplicações móveis, na web e nos arquivos de dados empresariais comuns. A pesquisa do Azure dá-lhe as ferramentas para criar uma experiência de pesquisa semelhantes às grandes sites web comercial.

Neste vídeo 9 minutos a partir do Gestor de programa Liam Cavanagh, saiba como integrar um motor de busca pode beneficiar da aplicação. Demonstrações curtas abrangem as principais funcionalidades na pesquisa do Azure e o aspeto de um fluxo de trabalho normal. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0 a 3 minutos abrange funcionalidades-chave e casos de utilização.
+ 3 a 4 minutos abrange o aprovisionamento de serviço. 
+ 4 a 6 minutos abrange o Assistente para importar dados utilizado para criar um índice utilizando o conjunto de dados incorporada de real de propriedade.
+ 9 de 6 minutos abrange o Explorador de pesquisa e várias consultas.


