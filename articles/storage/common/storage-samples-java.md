---
title: Exemplos de armazenamento do Azure utilizando Java | Microsoft Docs
description: "Ver, transferir e executar o código de exemplo e aplicações para o Storage do Azure. Detete a introdução aos exemplos para blobs, filas, tabelas e ficheiros, utilizando as bibliotecas de cliente do armazenamento de Java."
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: fd27e1ac9a773e7b0f5245aa74acdb0521cd098c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-samples-using-java"></a>Exemplos de armazenamento do Azure utilizando Java

## <a name="java-sample-index"></a>Índice de exemplo de Java

A tabela seguinte fornece uma descrição geral do nosso repositório de exemplos e os cenários abrangidos em cada amostra. Clique nas ligações para ver o código de exemplo correspondente no GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Ponto Final</th><th style="font-size:110%">Cenário</th><th style="font-size:110%">Código de exemplo</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Blob de acréscimo</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Blob de bloco</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Encriptação do Lado do Cliente</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Como começar a encriptação do lado do cliente do Azure em Java</a></td>
</tr> 
<tr> 
<td>Copiar Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Criar contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Eliminar o Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Eliminar o contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Blob metadados/propriedades/estatísticas</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>ACL/metadados/as propriedades do contentor</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Obter intervalos de página</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/CloudPageBlobTests.java">Exemplo de testes de BLOBs de páginas</a></td>
</tr> 
<tr> 
<td>Concessão de contentor do Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Lista/contentor do Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td>Blob de página</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/SasTests.java">Exemplo de testes SAS</a></td>
</tr>   
<tr> 
<td>Propriedades do Serviço</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr>           
<tr> 
<td>Blob de instantâneo</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Introdução ao serviço Blob do Azure em Java</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Ficheiro</b></td>
<td>Criar partilhas/diretórios/ficheiros</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr>
<tr> 
<td>Eliminar diretórios/partilhas/ficheiros</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Propriedades/metadados do diretório</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Transferir ficheiros</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Ficheiro de metadados/propriedades/métricas</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço de ficheiro</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Lista de diretórios e ficheiros</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr>
<tr> 
<td>Listar partilhas</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr>
<tr> 
<td>Partilhar metadados/propriedades/estatísticas</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Introdução ao serviço de ficheiros do Azure em Java</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Fila</b></td>
<td>Adicionar mensagem</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/queue/gettingstarted/QueueBasics.java">Exemplos de biblioteca de cliente de Java de armazenamento</a></td> 
</tr> 
<tr> 
<td>Encriptação do Lado do Cliente</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/encryption/queue/gettingstarted/QueueGettingStarted.java">Exemplos de biblioteca de cliente de Java de armazenamento</a></td> 
</tr> 
<tr> 
<td>Criar filas</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao serviço de fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Eliminar a fila de mensagens /</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao serviço de fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Observar mensagem</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao serviço de fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Fila ACL/metadados/estatísticas</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Introdução ao serviço de fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Propriedades do serviço fila</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Introdução ao serviço de fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Mensagem de actualização</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Introdução ao serviço de fila do Azure em Java</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Criar tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Introdução ao serviço de tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Eliminar uma entidade/tabela</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Introdução ao serviço de tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Entidade de intercalação/INSERT/substituir</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Exemplos de biblioteca de cliente de Java de armazenamento</a></td> 
</tr> 
<tr> 
<td>Entidades de consulta</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Introdução ao serviço de tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Tabelas de consulta</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Introdução ao serviço de tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Tabela ACL/propriedades</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableAdvanced.java">Introdução ao serviço de tabela do Azure em Java</a></td> 
</tr> 
<tr> 
<td>Entidade de atualização</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Exemplos de biblioteca de cliente de Java de armazenamento</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteca de exemplos de código do Azure

Para ver a biblioteca de exemplo completo, visite o [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage) biblioteca, o que inclui exemplos de armazenamento do Azure que pode transferir e executar localmente. A biblioteca de exemplo de código fornece código de exemplo no formato. zip. Em alternativa, pode procurar e clonar o repositório do GitHub para cada amostra.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Obter guias de introdução

Consulte os guias seguintes se estiver à procura para obter instruções sobre como instalar e começar a utilizar as bibliotecas de cliente de armazenamento do Azure.

* [Introdução ao serviço Blob do Azure em Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Introdução ao serviço de fila do Azure em Java](../storage-java-how-to-use-queue-storage.md)
* [Introdução ao serviço de tabela do Azure em Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Introdução ao serviço de ficheiros do Azure em Java](../storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Passos seguintes

Para informações sobre amostras para outros idiomas:

* .NET: [exemplos de armazenamento do azure através do .NET](../storage-samples-dotnet.md)
* Todos os outros idiomas: [exemplos de armazenamento do Azure](../storage-samples.md)
