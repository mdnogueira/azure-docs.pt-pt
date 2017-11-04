---
title: "A definição de propriedades e os metadados utilizando para importar/exportar do Azure - v1 | Microsoft Docs"
description: "Saiba como especificar as propriedades e metadados de ser definida nos blobs de destino ao executar a ferramenta de importação/exportação do Azure para preparar as suas unidades. Isto refere-se v1 da ferramenta de importação/exportação."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Definir propriedades e metadados durante o processo de importação
Quando executar a ferramenta de importação/exportação do Microsoft Azure para preparar as suas unidades, pode especificar propriedades e metadados de ser definida nos blobs de destino. Siga estes passos.  
  
1.  Para definir propriedades de blob, crie um ficheiro de texto no seu computador local que especifica os nomes de propriedades e valores.  
  
2.  Para definir os metadados do blob, crie um ficheiro de texto no seu computador local que especifica os valores e nomes de metadados.  
  
3.  Transmita o caminho completo para um ou ambos estes ficheiros para a ferramenta de importação/exportação do Azure como parte do `PrepImport` operação.  
  
> [!NOTE]
>  Quando especificar um ficheiro de metadados ou propriedades como parte de uma sessão de cópia, essas propriedades ou metadados estão definidos para todos os BLOBs que são importados como parte da sessão cópia. Se pretender especificar um conjunto diferente de propriedades ou metadados para alguns dos blobs a ser importados, terá de criar uma sessão de cópia separada com propriedades diferentes ou ficheiros de metadados.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Especificar as propriedades de Blob num ficheiro de texto  
Para especificar propriedades de blob, crie um ficheiro de texto local e incluem o XML que especifica os nomes das propriedades como elementos e valores de propriedade como valores. Eis um exemplo que especifica alguns valores de propriedade:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Guarde o ficheiro para uma localização local como `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Especifique os metadados do Blob num ficheiro de texto  
Da mesma forma, para especificar os metadados do blob, crie um ficheiro de texto local que especifica os nomes dos metadados como elementos e valores de metadados como valores. Eis um exemplo que especifica alguns valores de metadados:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Guarde o ficheiro para uma localização local como `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Criar uma sessão de cópia, incluindo as propriedades ou ficheiros de metadados  
Quando executar a ferramenta de importação/exportação do Azure para preparar a tarefa de importação, especifique o ficheiro de propriedades de linha de comandos utilizando a `PropertyFile` parâmetro. Especifique o ficheiro de metadados utilizando a linha de comandos a `/MetadataFile` parâmetro. Eis um exemplo que especifica ambos os ficheiros:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Passos seguintes

* [Formato de ficheiro dos metadados e propriedades do serviço Importar/Exportar](../storage-import-export-file-format-metadata-and-properties.md)
