---
title: "Fluxo de trabalho de exemplo para prep unidades de disco rígido para uma tarefa de importação do Azure para importar/exportar - v1 | Microsoft Docs"
description: "Consulte as instruções para o processo de conclusão da preparação unidades para uma tarefa de importação no serviço de importação/exportação do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 179c6bac9a2d9509baa0007a7008d75d0874a25e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação
Este tópico explica o processo de preparação unidades para uma tarefa de importação concluído.  
  
Neste exemplo importa os seguintes dados para uma conta de armazenamento do Azure de janela com o nome `mystorageaccount`:  
  
|Localização|Descrição|  
|--------------|-----------------|  
|H:\Video|Uma coleção de vídeos, 5 TB no total.|  
|H:\Photo|Uma coleção de fotografias, 30 GB no total.|  
|K:\Temp\FavoriteMovie.ISO|Imagem de disco A Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Uma coleção de ficheiros de música numa partilha de rede, 10 GB no total.|  
  
A tarefa de importação importa estes dados para os seguintes destinos na conta de armazenamento:  
  
|Origem|Diretório virtual de destino ou blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.Core.Windows.NET/Video|  
|H:\Photo|https://mystorageaccount.blob.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.Core.Windows.NET/Favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.Core.Windows.NET/Music|  
  
Com este mapeamento, o ficheiro `H:\Video\Drama\GreatMovie.mov` é importado para o blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Em seguida, para determinar quantos discos rígidos são necessários, o tamanho dos dados de computação:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Neste exemplo, dois discos rígidos de 3 TB devem ser suficientes. No entanto, dado que o diretório de origem `H:\Video` tem 5 TB de dados e a capacidade do disco rígido único é de apenas 3 TB, é necessário interromper `H:\Video` em dois diretórios menores: `H:\Video1` e `H:\Video2`, antes de executar o Microsoft Azure Ferramenta de importação/exportação. Este passo gera os seguintes diretórios de origem:  
  
|Localização|Tamanho|Diretório virtual de destino ou blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.blob.Core.Windows.NET/Video|  
|H:\Video2|2,5 TB|https://mystorageaccount.blob.Core.Windows.NET/Video|  
|H:\Photo|30 GB|https://mystorageaccount.blob.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.blob.Core.Windows.NET/Favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.blob.Core.Windows.NET/Music|  
  
 Apesar do `H:\Video`diretório tem foi dividido em dois diretórios, apontem para o diretório virtual de destino mesmo na conta de armazenamento. Desta forma, todos os ficheiros de vídeos são mantidos num único `video` contentor na conta de armazenamento.  
  
 Em seguida, os diretórios de origem de anterior uniformemente distribuídos em duas unidades de disco rígidas:  
  
||||  
|-|-|-|  
|Unidade de disco rígido|Diretórios de origem|Tamanho total|  
|Primeira unidade|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Segundo unidade|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Além disso, pode definir os metadados para todos os ficheiros seguintes:  
  
-   **UploadMethod:** serviço de importação/exportação do Windows Azure  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 1/10/2013  
  
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
  
-   **Tipo de conteúdo:** application/octet-stream.  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ = =  
  
-   **Cache-Control:** não cache  
  
Para definir estas propriedades, crie um ficheiro de texto, `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Agora, está pronto para executar a ferramenta de importação/exportação do Azure para preparar as duas unidades de disco rígidas. Tenha em atenção que:  
  
-   Primeira unidade de está montada como unidade X.  
  
-   A segunda unidade está montada como unidade Y.  
  
-   A chave da conta do storage `mystorageaccount` é `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Preparar o disco para importação quando previamente os dados são carregados
 
 Se os dados a importar já se encontra presentes no disco, utilize o sinalizador /skipwrite. O valor de /t e /srcdir deve ambas ponto para o disco a ser preparado para importação. Se todos os dados a serem importados não for o mesmo diretório virtual de destino ou raiz da conta de armazenamento, execute o mesmo comando para cada diretório de destino em separado, manter o valor de formado igual em todas as execuções.

>[!NOTE] 
>Não especifique /format como que irá eliminar os dados no disco. Pode especificar / encriptar ou /bk dependendo se o disco já estiver encriptado ou não. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Copiar sessões - primeiro a unidade

A unidade primeiro execute a ferramenta de importação/exportação do Azure duas vezes para copiar os dois origem diretórios:  

**Primeira sessão de cópia**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Segunda sessão de cópia**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Copiar sessões - segunda unidade
 
A segunda unidade executar a ferramenta de importação/exportação do Azure três vezes, uma vez cada para os diretórios de origem e, uma vez para o ficheiro de imagem autónomos Blu-Ray™):  
  
**Primeira sessão de cópia** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Segunda sessão de cópia**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Terceiro sessão de cópia**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Conclusão da sessão de cópia

Depois de concluíram as sessões de cópia, pode desligar duas unidades do computador de cópia e envie-os ao centro de dados adequado do Windows Azure. Carregar os ficheiros de dois diário de alterações, `FirstDrive.jrn` e `SecondDrive.jrn`, ao criar a tarefa de importação no [portal do Windows Azure](https://manage.windowsazure.com/).  
  
## <a name="next-steps"></a>Passos seguintes

* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Referência rápida para comandos utilizados com frequência](../storage-import-export-tool-quick-reference-v1.md) 
