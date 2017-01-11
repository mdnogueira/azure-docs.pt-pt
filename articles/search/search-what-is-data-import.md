---
title: Carregamento de dados na Azure Search | Documentos do Microsoft
description: "Saiba como carregar dados para um índice da Azure Search."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/09/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 72cc0d9ff35ff656a6134b52812b64c39a295a6f
ms.openlocfilehash: 53786d60d9971d9f976bf0f3ef4e40346c3101f4


---
# <a name="upload-data-to-azure-search"></a>Carregar dados para a Azure Search
> [!div class="op_single_selector"]
> * [Descrição geral](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

## <a name="data-upload-models-in-azure-search"></a>Modelos de carregamento de dados na Azure Search
Existem duas formas de preencher o índice da Azure Search com os seus dados. A primeira opção consiste no envio manual dos seus dados para o índice através da [API REST](search-import-data-rest-api.md) ou do [SDK .NET](search-import-data-dotnet.md) da Azure Search. A segunda opção consiste em [apontar uma origem de dados suportada](search-indexer-overview.md) para o índice da Azure Search e permitir que a Azure Search extraia automaticamente os seus dados para o serviço de pesquisa.

Este guia abrange apenas as instruções sobre a utilização do modelo de envio de carregamento de dados (suportado apenas na [API REST](search-import-data-rest-api.md) e no [SDK .NET](search-import-data-dotnet.md)), contudo, também pode saber mais acerca do modelo de extração a seguir. 

## <a name="push-data-to-an-index"></a>Enviar dados para um índice
Esta abordagem diz respeito ao envio através de programas dos seus dados para a Azure Search para os tornar disponíveis para pesquisa. Para aplicações com requisitos de latência muito baixos (por exemplo, caso sejam necessárias operações de pesquisa para estar sincronizado com bases de dados de inventário dinâmicas), o modelo de envio é a única opção.

Pode utilizar a [API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou o [SDK .NET](search-import-data-dotnet.md) para enviar dados para um índice. Atualmente não existe qualquer suporte de ferramentas para o envio de dados através do portal.

Esta abordagem é mais flexível do que o modelo de extração, pois pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, consoante o limite que ocorrer primeiro). O modelo de envio também permite carregar documentos para a Azure Search independentemente do local de armazenamento dos dados.

## <a name="pull-data-into-an-index"></a>Extrair dados para um índice
O modelo de extração pesquisa uma origem de dados suportada e carrega automaticamente os dados para o índice da Azure Search. Os indexadores, através do registo de alterações e eliminações aos documentos existentes, além do reconhecimento de novos documentos, suprimem a gestão ativa dos dados no índice.

Na Azure Search, esta função é implementada através de *indexadores*, atualmente disponíveis para [Blob Storage (pré-visualização)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [SQL Database do Azure e SQL Server em VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).

A funcionalidade de indexador está exposta no [portal do Azure](search-import-data-portal.md) e na [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations).




<!--HONumber=Dec16_HO2-->


