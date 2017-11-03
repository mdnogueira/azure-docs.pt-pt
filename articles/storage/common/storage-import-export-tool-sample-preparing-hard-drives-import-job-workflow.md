---
title: "Fluxo de trabalho de exemplo para prep unidades de disco rígido para uma tarefa de importação do Azure para importar/exportar | Microsoft Docs"
description: "Consulte as instruções para o processo de conclusão da preparação unidades para uma tarefa de importação no serviço de importação/exportação do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação

Este artigo explica o processo de preparação unidades para uma tarefa de importação concluído.

## <a name="sample-data"></a>Dados de exemplo

Neste exemplo importa os seguintes dados para uma conta de armazenamento do Azure com o nome `mystorageaccount`:

|Localização|Descrição|Tamanho dos dados|
|--------------|-----------------|-----|
|H:\Video\ |Uma coleção de vídeos|12 TB|
|H:\Photo\ |Uma coleção de fotografias|30 GB|
|K:\Temp\FavoriteMovie.ISO|Imagem de disco A Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Uma coleção de ficheiros de música numa partilha de rede|10 GB|

## <a name="storage-account-destinations"></a>Destinos de conta de armazenamento

A tarefa de importação irá importar os dados para os seguintes destinos na conta de armazenamento:

|Origem|Diretório virtual de destino ou blob|
|------------|-------------------------------------------|
|H:\Video\ |vídeo /|
|H:\Photo\ |fotografia /|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |música|

Com este mapeamento, o ficheiro `H:\Video\Drama\GreatMovie.mov` serão importados para o blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Determinar os requisitos de disco rígido

Em seguida, para determinar quantos discos rígidos são necessários, o tamanho dos dados de computação:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Neste exemplo, dois discos rígidos de 8TB devem ser suficientes. No entanto, dado que o diretório de origem `H:\Video` tem 12TB de dados e a capacidade do disco rígido único é apenas de 8TB, poderá especificar isto a seguir de forma a **driveset.csv** ficheiro:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
A ferramenta irá distribuem dados em duas unidades de disco rígidas uma forma otimizada.

## <a name="attach-drives-and-configure-the-job"></a>Anexar unidades e configurar a tarefa
Irá expor os dois discos para a máquina e crie volumes. Em seguida, criar **dataset.csv** ficheiro:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Além disso, pode definir os metadados para todos os ficheiros seguintes:

* **UploadMethod:** serviço de importação/exportação do Windows Azure
* **DataSetName:** SampleData
* **CreationDate:** 1/10/2013

Para definir os metadados para os ficheiros importados, crie um ficheiro de texto, `c:\WAImportExport\SampleMetadata.txt`, com o seguinte conteúdo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Também pode definir algumas propriedades para o `FavoriteMovie.ISO` blob:

* **Tipo de conteúdo:** application/octet-stream.
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ = =
* **Cache-Control:** não cache

Para definir estas propriedades, crie um ficheiro de texto, `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Execute a ferramenta de importação/exportação do Azure (WAImportExport.exe)

Agora, está pronto para executar a ferramenta de importação/exportação do Azure para preparar as duas unidades de disco rígidas.

**Para a primeira sessão:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Se todos os dados mais tem de ser adicionada, crie outro ficheiro de conjunto de dados (mesmo formato que Initialdataset).

**Para a segunda sessão:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Depois de concluíram as sessões de cópia, pode desligar duas unidades do computador de cópia e envie-os para o Centro de dados do Azure adequada. Deverá carregar os ficheiros de dois diário de alterações, `<FirstDriveSerialNumber>.xml` e `<SecondDriveSerialNumber>.xml`, ao criar a tarefa de importação no portal do Azure.

## <a name="next-steps"></a>Passos seguintes

* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Referência rápida para comandos utilizados com frequência](../storage-import-export-tool-quick-reference.md)
