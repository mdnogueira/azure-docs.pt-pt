---
title: Indexadores na Azure Search | Microsoft Docs
description: "Pesquise a base de dados SQL do Azure, o DocumentDB ou o armazenamento do Azure para extrair dados pesquisáveis e preencher um índice de Pesquisa do Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 6ff31940f3a4e7557e0caf3d9d3740590be3bc04
ms.openlocfilehash: 9a8a4454a0676c403356e9989665242978949011


---
# <a name="indexers-in-azure-search"></a>Indexadores na Pesquisa do Azure
> [!div class="op_single_selector"]
> * [Descrição geral](search-indexer-overview.md)
> * [Portal](search-import-data-portal.md)
> * [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
> * [DocumentDB](../documentdb/documentdb-search-indexer.md)
> * [Armazenamento de Blobs (pré-visualização)](search-howto-indexing-azure-blob-storage.md)
> * [Armazenamento de Tabelas (pré-visualização)](search-howto-indexing-azure-tables.md)
> 
> 

Um **indexador** na Pesquisa do Azure é um crawler que extrai dados pesquisáveis e metadados de uma origem de dados externa e preenche um índice com base nos mapeamentos campo a campo entre o índice e a sua origem de dados. Esta abordagem é por vezes referida como 'modelo de extração' porque o serviço obtém dados sem precisar de escrever qualquer código que envia dados para um índice.

Pode utilizar um indexador como único meio para ingestão de dados ou utilizar uma combinação de técnicas que incluem a utilização de um indexador para carregar apenas alguns dos campos no seu índice.

Pode executar os indexadores a pedido ou numa agenda de atualização de dados periódica que pode ser executada a cada quinze minutos. Atualizações mais frequentes requerem um modelo de push que atualize em simultâneo os dados na Pesquisa do Azure e a origem de dados externa.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerir indexadores
Para indexadores geralmente disponíveis como o Azure SQL ou o DocumentDB, pode criar e gerir os indexadores utilizando estas abordagens:

* [Portal > Assistente de Importação de Dados ](search-get-started-portal.md)
* [API REST do Serviço](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [SDK do .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Os indexadores de pré-visualização, como o Armazenamento de Tabelas ou Blobs do Azure, necessitam de código e de APIs de pré-visualização, como o [Azure Search Preview REST API para indexadores](search-api-indexers-2015-02-28-preview.md). Normalmente, as ferramentas de portal não estão disponíveis para as funcionalidades de pré-visualização.

## <a name="basic-configuration-steps"></a>Passos de configuração básica
Os indexadores podem oferecer funcionalidades que são exclusivas da origem de dados. Relativamente a isto, alguns aspetos de configuração do indexador ou da origem de dados irão variar consoante o tipo de indexador. No entanto, todos os indexadores partilham da mesma composição e requisitos básicos. Os passos que são comuns a todos os indexadores são abordados abaixo.

### <a name="step-1-create-an-index"></a>Passo 1: criar um índice
Um indexador irá automatizar algumas tarefas relacionadas com a ingestão de dados, mas a criação de um índice não é uma delas. Como pré-requisito, tem de ter um índice predefinido com campos que correspondam aos existentes na origem de dados externa. Para mais informações sobre a estruturação de um índice, consulte o artigo [Create an Index (Azure Search REST API) (Criar um índice (API REST do Azure Search))](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### <a name="step-2-create-a-data-source"></a>Passo 2: criar uma origem de dados
Um indexador obtém dados a partir de uma **origem de dados** que contém informações como uma cadeia de ligação. Atualmente são suportadas as seguintes origens de dados:

* [Base de Dados SQL ou SQL Server do Azure numa máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
* [DocumentDB](../documentdb/documentdb-search-indexer.md)
* [Armazenamento de Blobs do Azure (pré-visualização)](search-howto-indexing-azure-blob-storage.md), utilizado para extrair texto de PDF, documentos do Office, HTML ou XML
* [Armazenamento de Tabelas do Azure (pré-visualização)](search-howto-indexing-azure-tables.md)

As origens de dados são configuradas e geridas independentemente dos indexadores que as utilizam, o que significa que uma origem de dados pode ser utilizada por vários indexadores para carregar mais de um índice de cada vez. 

### <a name="step-3create-and-schedule-the-indexer"></a>Passo 3: criar e agendar o indexador
A definição de indexador é uma construção que especifica o índice, a origem de dados e uma agenda. Um indexador pode fazer referência a uma origem de dados de outro serviço, desde que essa origem de dados seja da mesma subscrição. Para mais informações sobre a estruturação de um indexador, consulte o artigo [Create Indexer (Azure Search REST API) (Criar um indexador (API REST do Azure Search))](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="next-steps"></a>Passos seguintes
Agora que tem uma noção básica, o passo seguinte é rever os requisitos e as tarefas específicas de cada tipo de origem de dados.

* [Base de Dados SQL ou SQL Server do Azure numa máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
* [DocumentDB](../documentdb/documentdb-search-indexer.md)
* [Armazenamento de Blobs do Azure (pré-visualização)](search-howto-indexing-azure-blob-storage.md), utilizado para extrair texto de PDF, documentos do Office, HTML ou XML
* [Armazenamento de Tabelas do Azure (pré-visualização)](search-howto-indexing-azure-tables.md)
* [Indexar blobs CSV com o indexador Blob do Azure Search (pré-visualização)](search-howto-index-csv-blobs.md)
* [Indexar blobs JSON com o indexador Blob do Azure Search (pré-visualização)](search-howto-index-json-blobs.md)




<!--HONumber=Nov16_HO2-->


