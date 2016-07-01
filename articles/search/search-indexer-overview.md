<properties
    pageTitle="Indexadores na Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa em nuvem alojado"
    description="Pesquise a base de dados SQL do Azure, o DocumentDB ou o armazenamento do Azure para extrair dados pesquisáveis e preencher um índice de Pesquisa do Azure."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="04/14/2016"
    ms.author="heidist"/>

# Indexadores na Pesquisa do Azure
> [AZURE.SELECTOR]
- [Descrição geral](search-indexer-overview.md)
- [Portal](search-import-data-portal.md)
- [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Armazenamento de Blobs (pré-visualização)](search-howto-indexing-azure-blob-storage.md)
- [Armazenamento de Tabelas (pré-visualização)](search-howto-indexing-azure-tables.md)

Um **indexador** na Pesquisa do Azure é um crawler que extrai dados pesquisáveis e metadados de uma origem de dados externa e preenche um índice com base nos mapeamentos campo a campo entre o índice e a sua origem de dados. Esta abordagem é por vezes referida como 'modelo de extração' porque o serviço obtém dados sem precisar de escrever qualquer código que envia dados para um índice.

Pode utilizar um indexador como único meio para ingestão de dados ou utilizar uma combinação de técnicas que incluem a utilização de um indexador para carregar apenas alguns dos campos no seu índice.

Os indexadores podem ser executados a pedido ou numa agenda de atualização de dados periódica que é frequentemente executada a cada quinze minutos. Atualizações mais frequentes requerem um modelo de push que atualize em simultâneo os dados na Pesquisa do Azure e a origem de dados externa.

Um indexador obtém dados a partir de uma **origem de dados** que contém informações como uma cadeia de ligação. Atualmente são suportadas as seguintes origens de dados:

- [Base de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (ou o SQL Server numa máquina virtual do Azure)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) (atualmente em pré-visualização. Extrai o texto de PDF, documentos do Office, HTML, XML.)
- [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md) (atualmente em pré-visualização)

As origens de dados são configuradas e geridas independentemente dos indexadores que as utilizam, o que significa que uma origem de dados pode ser utilizada por vários indexadores para carregar mais de um índice de cada vez. 

Tanto o [SDK .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) e a [API REST do Serviço](https://msdn.microsoft.com/library/azure/dn946891.aspx) suportam a gestão de indexadores e origens de dados. 

Em alternativa, também pode configurar um indexador no portal quando utilizar o assistente **Importar dados**. Consulte o artigo [Introdução à Pesquisa do Azure no portal](search-get-started-portal) para um tutorial rápido, utilizando dados de exemplo e o indexador DocumentDB para criar e carregar um índice utilizando o assistente.






<!--HONumber=Jun16_HO2-->


