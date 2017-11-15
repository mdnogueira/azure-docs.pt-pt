---
title: "Introdução ao Blob storage do Azure | Microsoft Docs"
description: "Introdução ao Blob storage do Azure"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: tamram
ms.openlocfilehash: 6059ce809e7a8b2115e391d9db5b5b0a8626109e
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="introduction-to-blob-storage"></a>Introdução ao Blob storage

O Blob Storage do Azure é um serviço para armazenar grandes quantidades de dados de objetos não estruturados, como texto ou dados binários, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Pode utilizar o armazenamento de Blobs para expor publicamente os dados ao mundo ou para armazenar dados da aplicação em privado.

Utilizações comuns do armazenamento de Blobs:

* Entrega de imagens ou documentos diretamente a um browser
* Armazenamento de ficheiros para acesso distribuído
* Transmissão de áudio e vídeo
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure

## <a name="blob-service-concepts"></a>Conceitos do serviço Blob

O serviço Blob contém os seguintes componentes:

![Arquitetura de blob](./media/storage-blobs-introduction/blob1.png)

* **Conta de Armazenamento:** todos os acessos ao Storage do Azure são efetuados através de uma conta de armazenamento. Esta conta de armazenamento pode ser um **conta do storage para fins gerais** ou um **conta de armazenamento de BLOBs** que é especializada para armazenar objetos/blobs. Consulte [About Azure storage accounts (Acerca das contas de armazenamento do Azure)](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter mais informações.

* **Contentor:** um contentor fornece um agrupamento de um conjunto de blobs. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs. Tenha em atenção que o nome do contentor tem de ser em minúsculas.

* **Blob:** um ficheiro de qualquer tipo e tamanho. O Storage do Azure oferece três tipos de blobs: blobs de blocos, blobs de páginas e blobs de acréscimo.
  
    Os *blobs de blocos* são ideais para armazenar ficheiros de texto ou binários, como documentos e ficheiros multimédia. Os *blobs de acréscimo* são semelhantes aos blobs de blocos na medida em que são constituídos por blocos, mas estão otimizados para operações de acréscimo, sendo pois úteis para cenários de registo. Um único blob de blocos pode conter até 50.000 blocos com um máximo de 100 MB cada, para um tamanho total ligeiramente acima de 4.75 TB (100 MB X 50.000). Um único blob de acréscimo pode conter até 50.000 blocos com um máximo de 4 MB cada, para um tamanho total ligeiramente acima de 195 GB (4 MB X 50.000).
  
    *Blobs de páginas* pode ser até 8 TB de tamanho e são mais eficientes para operações de leitura/escrita frequentes. As Virtual Machines do Azure utilizam blobs de páginas como discos de dados e SO.
  
    Para obter detalhes sobre os nomes dos contentores e dos blobs, veja [Naming and Referencing Containers, Blobs, and Metadata (Nomenclatura e Referência de Contentores, Blobs e Metadados)](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Introdução ao Blob storage através do .NET](storage-dotnet-how-to-use-blobs.md)
* [Exemplos de armazenamento do Azure através do .NET](../common/storage-samples-dotnet.md)
* [Exemplos de armazenamento do Azure utilizando Java](../common/storage-samples-java.md)
