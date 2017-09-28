# Descrição geral
## [O que é o Azure Search?](search-what-is-azure-search.md)
# Introdução
## [Escolher um SKU](search-sku-tier.md)
## [Criar um serviço](search-create-service-portal.md)
## [Criar um índice](search-what-is-an-index.md)
### [Portal do Azure](search-create-index-portal.md)
### [.NET](search-create-index-dotnet.md)
### [REST](search-create-index-rest-api.md)
## [Adicionar dados](search-what-is-data-import.md)
### [Portal do Azure](search-import-data-portal.md)
### [.NET](search-import-data-dotnet.md)
### [REST](search-import-data-rest-api.md)
## [Pesquisar um índice](search-query-overview.md)
### [Portal do Azure](search-explorer.md)
### [.NET](search-query-dotnet.md)
### [REST](search-query-rest-api.md)
# Tutoriais
## [.NET](search-howto-dotnet-sdk.md)
## [Pré-visualização de sinónimos de .NET](search-synonyms-tutorial-sdk.md)
## [Portal](search-get-started-portal.md)
## [Node.js](search-get-started-nodejs.md)
## [Java](search-get-started-java.md)
# Procedimento
## Planear e conceber
### [Limites do serviço](search-limits-quotas-capacity.md)
### [Escalabilidade do serviço](search-capacity-planning.md)
### [Padrões de conceção para multi-inquilinos](search-modeling-multitenant-saas-applications.md)

## Programar
### [Versões da API](search-api-versions.md)
### [Atualizar o SDK](search-dotnet-sdk-migration.md)
### [Atualizar a API REST](search-api-migration.md)
### [Modelar tipos de dados complexos](search-howto-complex-data-types.md)
### [Processar atualizações em simultâneo](search-howto-concurrency.md)
### [Exemplos de código](https://azure.microsoft.com/resources/samples/?service=search)

## Gerir
### Administrar o Azure Search
#### [Portal do Azure](search-manage.md)
#### [Azure PowerShell](search-manage-powershell.md)
### [Monitorizar a utilização e as estatísticas](search-monitor-usage.md)
### [Procurar análises de tráfego](search-traffic-analytics.md)
### [Desempenho e otimização](search-performance-optimization.md)
## Carregar dados
### [Descrição geral do Indexador](search-indexer-overview.md)
### [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
### [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
### [Indexador do Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
### [Indexador da BD do Cosmos para o Azure](search-howto-index-documentdb.md)
### [Blobs CSV de Indexação](search-howto-index-csv-blobs.md)
### [Blobs JSON de Indexação](search-howto-index-json-blobs.md)
### [Configurar ligações de indexador ao SQL Server em VMs do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
### [Mapeamentos de campo nos indexadores](search-indexer-field-mappings.md)
##  Pesquisa
### [Como funciona a pesquisa em texto completo](search-lucene-query-architecture.md)
### Construção da Consulta
#### [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
#### [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
#### [Exemplos de consulta de sintaxe Lucene](search-query-lucene-examples.md)
#### [Sintaxe de expressão de filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
### Analisadores no Azure Search
#### [Descrição geral](search-analyzers.md)
#### [Analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support)
#### [Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)
### [Resultados de paginação](search-pagination-page-layout.md)
### [Classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
### [Sugestões](https://docs.microsoft.com/rest/api/searchservice/suggesters)
### [Navegação por facetas](search-faceted-navigation.md)
### [Pré-visualização de sinónimos](search-synonyms.md)

# Referência

## [.NET](/dotnet/api/?term=microsoft.azure.search)
## [.NET (Gestão)](/dotnet/api/?term=microsoft.azure.management.search)
## [Python (Gestão)](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.search.html)
## [REST](/rest/api/searchservice)
## [REST (Gestão)](/rest/api/searchmanagement)
## [REST do serviço (Pré-visualização)](search-api-2015-02-28-preview.md)

# Recursos

## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [Perguntas Frequentes](search-faq-frequently-asked-questions.md)
## [Preços](https://azure.microsoft.com/pricing/details/search/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=search)
## Cursos e tutoriais
### [Vídeos e tutoriais](search-video-demo-tutorial-list.md)
### [Academia virtual](https://mva.microsoft.com/training-courses/using-windows-azure-search-10540?l=ADkxnd97_9304984382)
## Sites de demonstração
### [Demonstração do Search Analyzer](http://alice.unearth.ai/)
### [Aplicações de demonstração ao vivo](https://searchsamples.azurewebsites.net/)
### [Aplicação de listagem de trabalhos](http://aka.ms/azjobsdemo)
## Parceiros e comunidade
### [GitHub do Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)
### [Fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureSearch)
### [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-search)
### [blog: Dados relacionais de modelo](http://blogs.technet.com/b/onsearch/archive/2015/09/08/modeling-the-adventureworks-inventory-database-for-azure-search.aspx)
### [[blog: Facetamento de vários níveis](http://blogs.technet.com/b/onsearch/archive/2015/09/09/multi-level-taxonomy-facets-in-azure-search.aspx)



