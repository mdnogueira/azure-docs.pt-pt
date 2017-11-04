---
title: "Formato de ficheiro de manifesto de importação/exportação do Azure | Microsoft Docs"
description: "Saiba mais sobre o formato do ficheiro de manifesto unidade que descreve o mapeamento entre os blobs no armazenamento de Blobs do Azure e os ficheiros numa unidade numa importação ou exportação tarefa no serviço de importação/exportação."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c1857eb94fba13c30e7f07669616f5d0ab9953f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-manifest-file-format"></a>Formato de ficheiro de manifesto de serviço de importação/exportação do Azure
O ficheiro de manifesto unidade descreve o mapeamento entre os blobs no armazenamento de Blobs do Azure e os ficheiros numa unidade que inclui uma tarefa de importação ou exportação. Para uma operação de importação, o ficheiro de manifesto é criado como parte do processo de preparação de unidade e é armazenado na unidade antes da unidade é enviada para o Centro de dados do Azure. Durante uma operação de exportação, o manifesto é criado e armazenado na unidade pelo serviço de importação/exportação do Azure.  
  
Para ambos importar e exportar tarefas, o ficheiro de manifesto da unidade é armazenado na unidade de importação ou exportação; não ser transmitido para o serviço através de todas as operações de API.  
  
O seguinte descreve o formato geral de um ficheiro de manifesto de unidade:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Atributos e elementos XML manifesto

Os elementos de dados e atributos com o formato do XML de manifesto unidade estão especificados na seguinte tabela.  
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Elemento raiz|O elemento de raiz do ficheiro de manifesto. Todos os outros elementos no ficheiro estão sob este elemento.|  
|`Version`|Atributo, cadeia|A versão do ficheiro de manifesto.|  
|`Drive`|Aninhada elemento XML|Contém o manifesto para cada unidade.|  
|`DriveId`|Cadeia|O identificador exclusivo de unidade para a unidade. O identificador de unidade encontra-se ao consultar a unidade para o número de série. O número de série da unidade está normalmente impresso no exterior da unidade bem. O `DriveID` elemento tem de aparecer antes de qualquer `BlobList` elemento no ficheiro de manifesto.|  
|`StorageAccountKey`|Cadeia|É necessário para importar tarefas se apenas se `ContainerSas` não está especificado. A chave de conta para a conta de armazenamento do Azure associado ao trabalho.<br /><br /> Este elemento é omitido do manifesto de uma operação de exportação.|  
|`ContainerSas`|Cadeia|É necessário para importar tarefas se apenas se `StorageAccountKey` não está especificado. O contentor de SAS para aceder aos blobs associados ao trabalho. Consulte [colocar tarefa](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) para o seu formato. Este elemento é omitido do manifesto de uma operação de exportação.|  
|`ClientCreator`|Cadeia|Especifica o cliente que criou o ficheiro XML. Este valor não é interpretado pelo serviço de importação/exportação.|  
|`BlobList`|Aninhada elemento XML|Contém uma lista de blobs que fazem parte da importação ou exportação tarefa. Cada blob numa lista de blob partilha a mesma metadados e propriedades.|  
|`BlobList/MetadataPath`|Cadeia|Opcional. Especifica o caminho relativo de um ficheiro no disco que contém os metadados predefinidos que serão definidos em blobs na lista de BLOBs para uma operação de importação. Estes metadados podem ser substituído, opcionalmente, de forma blob por BLOBs.<br /><br /> Este elemento é omitido do manifesto de uma operação de exportação.|  
|`BlobList/MetadataPath/@Hash`|Atributo, cadeia|Especifica o valor de hash MD5 Base16 codificados para o ficheiro de metadados.|  
|`BlobList/PropertiesPath`|Cadeia|Opcional. Especifica o caminho relativo de um ficheiro no disco que contém as propriedades predefinidas que serão definidas em blobs na lista de BLOBs para uma operação de importação. Estas propriedades podem ser substituídas, opcionalmente, de forma blob por BLOBs.<br /><br /> Este elemento é omitido do manifesto de uma operação de exportação.|  
|`BlobList/PropertiesPath/@Hash`|Atributo, cadeia|Especifica o valor de hash MD5 Base16 codificados para o ficheiro de propriedades.|  
|`Blob`|Aninhada elemento XML|Contém informações sobre cada blob em cada lista de blob.|  
|`Blob/BlobPath`|Cadeia|O URI relativo para o blob, começando com o nome do contentor. Se os BLOBs no contentor de raiz, tem de começar com `$root`.|  
|`Blob/FilePath`|Cadeia|Especifica o caminho relativo para o ficheiro na unidade. Para as tarefas de exportação, será utilizado o caminho do blob para o caminho de ficheiro se for possível; *por exemplo,*, `pictures/bob/wild/desert.jpg` serão exportados para `\pictures\bob\wild\desert.jpg`. No entanto, devido a limitações de nomes NTFS, um blob pode ser exportado para um ficheiro com um caminho que não assemelhar-se o caminho de blob.|  
|`Blob/ClientData`|Cadeia|Opcional. Contém os comentários do cliente. Este valor não é interpretado pelo serviço de importação/exportação.|  
|`Blob/Snapshot`|DateTime|Opcional para as tarefas de exportação. Especifica o identificador de instantâneo de um instantâneo de blob exportado.|  
|`Blob/Length`|Número inteiro|Especifica o comprimento total do blob em bytes. O valor pode ser até 200 GB para um blob de bloco e até 1 TB para um blob de página. Para um blob de página, este valor tem de ser um múltiplo de 512.|  
|`Blob/ImportDisposition`|Cadeia|Opcional para tarefas de importação, omitidas para as tarefas de exportação. Esta ação Especifica a forma como o serviço de importação/exportação deve processar o caso de uma tarefa de importação onde um blob com o mesmo nome já existe. Se este valor for omitido do manifesto de importação, o valor predefinido é `rename`.<br /><br /> Os valores para este elemento incluem:<br /><br /> -   `no-overwrite`: Se um blob de destino já existir com o mesmo nome, a operação de importação irá ignorar este ficheiro a importar.<br />-   `overwrite`: Quaisquer blob de destino existente está substituído completamente pelo ficheiro importado recentemente.<br />-   `rename`: Novo blob será carregado com um nome de modificação.<br /><br /> A regra de mudança de nome é o seguinte:<br /><br /> -Se o nome do blob não contém um ponto, um novo nome é gerado, acrescentando `(2)` para o nome de blob original; se este novo nome também está em conflito com um nome de blob existente, em seguida, `(3)` é acrescentada em vez de `(2)`; e assim sucessivamente.<br />-Se o nome do blob contém um ponto, a parte seguinte do último ponto é considerada o nome de extensão. Semelhante para o procedimento acima, `(2)` é inserido antes do último ponto para gerar um novo nome; se o novo nome ainda está em conflito com um blob nome, em seguida, o serviço tenta `(3)`, `(4)`, e assim sucessivamente, até é encontrado um nome não esteja em conflito.<br /><br /> Alguns exemplos:<br /><br /> O blob `BlobNameWithoutDot` vai ser mudado para:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> O blob `Seattle.jpg` vai ser mudado para:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Aninhada elemento XML|Necessário para um blob de página.<br /><br /> Para uma importação operação especifica uma lista de intervalos de byte de um ficheiro a importar. Cada intervalo de páginas é descrito por um desvio e comprimento no ficheiro de origem que descreve o intervalo de páginas, juntamente com um hash MD5 da região. O `Hash` é necessário o atributo de um intervalo de página. O serviço irá validar que o hash dos dados no blob corresponda ao hash MD5 calculado do intervalo de página. Qualquer número de intervalos de página pode ser utilizado para descrever um ficheiro de importação, com o total de tamanho até 1 TB. Todos os intervalos de página tem de ser ordenados por deslocamento e sem sobreposições são permitidas.<br /><br /> Exportação da operação especifica um conjunto de intervalos de byte de um blob que foram exportados para a unidade.<br /><br /> Em conjunto os intervalos de página podem abranger apenas secundárias intervalos de um blob ou um ficheiro.  A parte restante do ficheiro não abrangido por qualquer intervalo de páginas é esperada e respetivo conteúdo pode ser definido.|  
|`PageRange`|Elemento XML|Representa um intervalo de páginas.|  
|`PageRange/@Offset`|Atributo de número inteiro|Especifica o deslocamento no ficheiro de transferência e o blob onde inicia o intervalo de páginas especificado. Este valor tem de ser um múltiplo de 512.|  
|`PageRange/@Length`|Atributo de número inteiro|Especifica o comprimento do intervalo de página. Este valor tem de ser um múltiplo de 512 e não mais de 4 MB.|  
|`PageRange/@Hash`|Atributo, cadeia|Especifica o valor de hash MD5 Base16 codificados para o intervalo de página.|  
|`BlockList`|Aninhada elemento XML|Necessário para um blob de blocos com blocos com nome.<br /><br /> Para uma operação de importação, a lista de bloqueios Especifica um conjunto de blocos que serão importados para o Storage do Azure. Para uma operação de exportação, a lista de bloqueios Especifica onde cada bloco tenha sido armazenado no ficheiro de disco de exportação. Cada bloco é descrito por um desvio no ficheiro e um comprimento do bloco; cada bloco além disso é designado por um atributo de ID de bloco e contém um hash MD5 para o bloco. Até 50 000 blocos pode ser utilizado para descrever um blob.  Todos os blocos têm de ser ordenados por deslocamento e em conjunto deve abranger o intervalo completo do ficheiro, *ou seja,*, não tem de existir nenhum intervalo entre blocos. Se o blob é mais do que 64 MB, o bloco IDs para cada bloco tem de ser ausência de todos os ou todos os presentes. Os IDs de bloco têm de ser cadeias com codificação Base64. Consulte [colocar bloco](/rest/api/storageservices/put-block) para obter os requisitos para IDs de bloco.|  
|`Block`|Elemento XML|Representa um bloco.|  
|`Block/@Offset`|Atributo de número inteiro|Especifica o deslocamento onde inicia o bloco especificado.|  
|`Block/@Length`|Atributo de número inteiro|Especifica o número de bytes no bloco de; Este valor tem de ser não mais de 4MB.|  
|`Block/@Id`|Atributo, cadeia|Especifica uma cadeia que representa o ID do bloco para o bloco.|  
|`Block/@Hash`|Atributo, cadeia|Especifica o hash MD5 codificado Base16 do bloco.|  
|`Blob/MetadataPath`|Cadeia|Opcional. Especifica o caminho relativo de um ficheiro de metadados. Durante a importação, os metadados está definido no blob de destino. Durante uma operação de exportação, os metadados o blob são armazenados no ficheiro de metadados na unidade.|  
|`Blob/MetadataPath/@Hash`|Atributo, cadeia|Especifica o hash MD5 Base16 com codificação de ficheiro de metadados do blob.|  
|`Blob/PropertiesPath`|Cadeia|Opcional. Especifica o caminho relativo de um ficheiro de propriedades. Durante a importação, as propriedades forem definidas no blob de destino. Durante uma operação de exportação, as propriedades de blob são armazenadas no ficheiro de propriedades na unidade.|  
|`Blob/PropertiesPath/@Hash`|Atributo, cadeia|Especifica o hash MD5 codificado Base16 do ficheiro de propriedades do blob.|  
  
## <a name="next-steps"></a>Passos seguintes
 
* [API de REST do Storage para importar/exportar](/rest/api/storageimportexport/)
