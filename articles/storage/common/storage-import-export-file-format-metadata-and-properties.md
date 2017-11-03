---
title: Do Azure para importar/exportar metadados e as propriedades de formato de ficheiro de | Microsoft Docs
description: "Saiba como especificar os metadados e propriedades de um ou mais blobs que fazem parte de uma importação ou exportação tarefa."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3f728ad94cdcbd32092b677f11a737ae91376720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Do Azure para importar/exportar serviço metadados e as propriedades de formato de ficheiro
Pode especificar propriedades de um ou mais blobs e metadados como parte de uma tarefa de importação ou uma tarefa de exportação. Para definir os metadados ou propriedades de blobs a ser criadas como parte de uma tarefa de importação, forneça um ficheiro de metadados ou propriedades no disco rígido que contém os dados a ser importados. Para uma tarefa de exportação, metadados e as propriedades são escritas num ficheiro de metadados ou propriedades que está incluído no disco rígido devolvido para si.  
  
## <a name="metadata-file-format"></a>Formato de ficheiro de metadados  
O formato de ficheiro de metadados é o seguinte:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`Metadata`|Elemento raiz|O elemento de raiz do ficheiro de metadados.|  
|`metadata-name`|Cadeia|Opcional. O elemento XML Especifica o nome dos metadados do blob e o respetivo valor Especifica o valor da definição de metadados.|  
  
## <a name="properties-file-format"></a>Formato de ficheiro de propriedades  
O formato de um ficheiro de propriedades é o seguinte:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`Properties`|Elemento raiz|O elemento de raiz do ficheiro de propriedades.|  
|`Last-Modified`|Cadeia|Opcional. O tempo de última modificação de mensagens em fila para o blob. Para as tarefas de exportação apenas.|  
|`Etag`|Cadeia|Opcional. Valor de ETag o blob. Para as tarefas de exportação apenas.|  
|`Content-Length`|Cadeia|Opcional. O tamanho do blob em bytes. Para as tarefas de exportação apenas.|  
|`Content-Type`|Cadeia|Opcional. O tipo de conteúdo do blob.|  
|`Content-MD5`|Cadeia|Opcional. Hash de MD5 o blob.|  
|`Content-Encoding`|Cadeia|Opcional. Conteúdo do blob codificação.|  
|`Content-Language`|Cadeia|Opcional. Idioma de conteúdo do blob.|  
|`Cache-Control`|Cadeia|Opcional. A cadeia de controlo de cache para o blob.|  

## <a name="next-steps"></a>Passos seguintes

Consulte [defina as propriedades de blob](/rest/api/storageservices/set-blob-properties), [definir metadados do Blob](/rest/api/storageservices/set-blob-metadata), e [definição e ao obter propriedades e os metadados para recursos do blob](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) para regras de detalhado sobre a definição de metadados de blob e propriedades.
