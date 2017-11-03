---
title: "Formato de ficheiro de registo de importação/exportação do Azure | Microsoft Docs"
description: "Saiba mais sobre o formato dos ficheiros de registo criado quando são executados os passos para uma tarefa de serviço de importação/exportação."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 16234ccaf13ce1d85cfd207ed4734e683070faa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-log-file-format"></a>Formato do ficheiro de registo de serviço do Azure para importar/exportar
Quando o serviço de importação/exportação do Microsoft Azure efetua uma ação num disco como parte de uma tarefa de importação ou uma tarefa de exportação, os registos são escritos para bloquear os blobs na conta do storage associadas com essa tarefa.  
  
Existem dois registos que podem ser escritos pelo serviço de importação/exportação:  
  
-   O registo de erros sempre é gerado na eventualidade de ocorrer um erro.  
  
-   O registo verboso não está ativado por predefinição, mas pode ser ativado definindo a `EnableVerboseLog` propriedade um [colocar tarefa](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) ou [propriedades da tarefa de atualização](/rest/api/storageimportexport/jobs#Jobs_Update) operação.  
  
## <a name="log-file-location"></a>Localização do ficheiro de registo  
Os registos são escritos para bloquear os blobs no contentor ou diretório virtual especificado pelo `ImportExportStatesPath` definição, que podem ser definidas num `Put Job` operação. A localização a que os registos são escritos depende de como a autenticação é especificada para a tarefa, juntamente com o valor especificado para `ImportExportStatesPath`. Autenticação para a tarefa pode ser especificada através de uma chave de conta de armazenamento ou de um contentor SAS (assinatura de acesso partilhado).  
  
O nome do diretório virtual ou contentor pode a ser o nome predefinido do `waimportexport`, ou outro contentor ou nome do diretório virtual que especificou.  
  
A tabela abaixo mostra as opções possíveis:  
  
|Método de Autenticação|O valor de `ImportExportStatesPath`elemento|Localização de Blobs de registo|  
|---------------------------|----------------------------------------------|---------------------------|  
|Chave de conta de armazenamento|Valor predefinido|Um contentor com o nome `waimportexport`, que é o contentor predefinido. Por exemplo:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Chave de conta de armazenamento|Valor de utilizador especificado|Um contentor com o nome de utilizador. Por exemplo:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Contentor SAS|Valor predefinido|Um diretório virtual com o nome `waimportexport`, que é o nome predefinido, sob o contentor especificado na SAS.<br /><br /> Por exemplo, se a SAS especificado para a tarefa é `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, em seguida, a localização do registo seria`https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Contentor SAS|Valor de utilizador especificado|Um diretório virtual com o nome de utilizador, sob o contentor especificado na SAS.<br /><br /> Por exemplo, se a SAS especificado para a tarefa é `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, e o nome do diretório virtual especificados `mylogblobs`, em seguida, a localização do registo seria `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Pode obter o URL para os registos verbosos e erro ao chamar o [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operação. Os registos estão disponíveis após a conclusão do processamento da unidade.  
  
## <a name="log-file-format"></a>Formato de ficheiro de registo  
O formato para ambos os registos é o mesmo: um blob que contém descrições de XML dos eventos que ocorreram durante a cópia de blobs entre o disco rígido e a conta do cliente.  
  
O registo verboso contém informações sobre o estado da operação de cópia de cada blob (para uma tarefa de importação) ou o ficheiro (para uma tarefa de exportação), enquanto que o registo de erros contém apenas as informações de blobs ou ficheiros que encontrou erros durante a importação ou tarefa de exportação.  
  
O formato de registo verboso é mostrado abaixo. O registo de erros tem a mesma estrutura, mas filtra operações com êxito.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

A tabela seguinte descreve os elementos do ficheiro de registo.  
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`DriveLog`|Elemento XML|Representa um registo de unidade.|  
|`Version`|Atributo, cadeia|A versão do formato de registo.|  
|`DriveId`|Cadeia|Número de série de hardware a unidade.|  
|`Status`|Cadeia|Estado do processamento de unidade. Consulte o `Drive Status Codes` tabela abaixo para obter mais informações.|  
|`Blob`|Aninhada elemento XML|Representa um blob.|  
|`Blob/BlobPath`|Cadeia|O URI do blob.|  
|`Blob/FilePath`|Cadeia|O caminho relativo para o ficheiro na unidade.|  
|`Blob/Snapshot`|DateTime|A versão de instantâneos do blob, para apenas uma tarefa de exportação.|  
|`Blob/Length`|Número inteiro|O comprimento total do blob em bytes.|  
|`Blob/LastModified`|DateTime|A data/hora que o blob foi modificado pela última vez, para apenas uma tarefa de exportação.|  
|`Blob/ImportDisposition`|Cadeia|A disposição de importação do blob, apenas uma tarefa de importação.|  
|`Blob/ImportDisposition/@Status`|Atributo, cadeia|O estado da disposição de importação.|  
|`PageRangeList`|Aninhada elemento XML|Representa uma lista de intervalos de página para um blob de página.|  
|`PageRange`|Elemento XML|Representa um intervalo de páginas.|  
|`PageRange/@Offset`|Atributo de número inteiro|Deslocamento do intervalo de páginas de início no blob.|  
|`PageRange/@Length`|Atributo de número inteiro|Comprimento em bytes do intervalo de páginas.|  
|`PageRange/@Hash`|Atributo, cadeia|Com codificação Base16 hash MD5 do intervalo de página.|  
|`PageRange/@Status`|Atributo, cadeia|Estado de processamento o intervalo de páginas.|  
|`BlockList`|Aninhada elemento XML|Representa uma lista de blocos para um blob de blocos.|  
|`Block`|Elemento XML|Representa um bloco.|  
|`Block/@Offset`|Atributo de número inteiro|A partir de deslocamento do bloco do blob.|  
|`Block/@Length`|Atributo de número inteiro|Comprimento em bytes, o bloco.|  
|`Block/@Id`|Atributo, cadeia|O ID do bloco.|  
|`Block/@Hash`|Atributo, cadeia|Com codificação Base16 hash MD5 do bloco.|  
|`Block/@Status`|Atributo, cadeia|Estado de processamento o bloco.|  
|`Metadata`|Aninhada elemento XML|Representa os metadados do blob.|  
|`Metadata/@Status`|Atributo, cadeia|Estado de processamento dos metadados do blob.|  
|`Metadata/GlobalPath`|Cadeia|Caminho relativo para o ficheiro de metadados globais.|  
|`Metadata/GlobalPath/@Hash`|Atributo, cadeia|Com codificação Base16 hash MD5 do ficheiro de metadados globais.|  
|`Metadata/Path`|Cadeia|Caminho relativo para o ficheiro de metadados.|  
|`Metadata/Path/@Hash`|Atributo, cadeia|Com codificação Base16 hash MD5 do ficheiro de metadados.|  
|`Properties`|Aninhada elemento XML|Representa as propriedades de blob.|  
|`Properties/@Status`|Atributo, cadeia|Estado do processamento de propriedades blob, por exemplo, ficheiro não encontrado; foi concluída.|  
|`Properties/GlobalPath`|Cadeia|Caminho relativo para o ficheiro de propriedades globais.|  
|`Properties/GlobalPath/@Hash`|Atributo, cadeia|Com codificação Base16 hash MD5 do ficheiro global propriedades.|  
|`Properties/Path`|Cadeia|Caminho relativo para o ficheiro de propriedades.|  
|`Properties/Path/@Hash`|Atributo, cadeia|Com codificação Base16 hash MD5 do ficheiro de propriedades.|  
|`Blob/Status`|Cadeia|Estado de processamento o blob.|  
  
# <a name="drive-status-codes"></a>Códigos de estado de unidade  
A tabela seguinte lista os códigos de estado para uma unidade de processamento.  
  
|Código de estado|Descrição|  
|-----------------|-----------------|  
|`Completed`|A unidade concluiu o processamento sem erros.|  
|`CompletedWithWarnings`|A unidade concluiu o processamento com avisos num ou mais blobs pelos dispositions de importação especificados para os blobs.|  
|`CompletedWithErrors`|O disco foi concluída com erros de um ou mais blobs ou segmentos.|  
|`DiskNotFound`|É encontrado nenhum disco na unidade.|  
|`VolumeNotNtfs`|O primeiro volume de dados no disco não está no formato NTFS.|  
|`DiskOperationFailed`|Ocorreu uma falha desconhecida ao realizar operações na unidade.|  
|`BitLockerVolumeNotFound`|Não foi encontrado nenhum volume encryptable do BitLocker.|  
|`BitLockerNotActivated`|O BitLocker não está ativado no volume.|  
|`BitLockerProtectorNotFound`|O protetor de chave de palavra-passe numérica não existe no volume.|  
|`BitLockerKeyInvalid`|A palavra-passe numérica fornecido não é possível desbloquear o volume.|  
|`BitLockerUnlockVolumeFailed`|Falha desconhecida tiver ocorrido durante a tentativa de desbloquear o volume.|  
|`BitLockerFailed`|Ocorreu uma falha desconhecida ao executar operações de BitLocker.|  
|`ManifestNameInvalid`|O nome de ficheiro de manifesto é inválido.|  
|`ManifestNameTooLong`|O nome de ficheiro de manifesto é demasiado longo.|  
|`ManifestNotFound`|O ficheiro de manifesto não foi encontrado.|  
|`ManifestAccessDenied`|É negado o acesso ao ficheiro de manifesto.|  
|`ManifestCorrupted`|O ficheiro de manifesto está danificado (o conteúdo não corresponde ao respetivo hash).|  
|`ManifestFormatInvalid`|O conteúdo de manifesto não é compatível com o formato requerido.|  
|`ManifestDriveIdMismatch`|O ID de unidade no ficheiro de manifesto não corresponde a uma leitura da unidade.|  
|`ReadManifestFailed`|Ocorreu uma falha de e/s do disco ao ler o manifesto.|  
|`BlobListFormatInvalid`|O blob de lista de BLOBs de exportação não é compatível com o formato requerido.|  
|`BlobRequestForbidden`|Acesso para os blobs na conta de armazenamento é proibido. Isto pode dever-se a chave de conta de armazenamento inválido ou contentor SAS.|  
|`InternalError`|E, Ocorreu um erro interno ao processar a unidade.|  
  
## <a name="blob-status-codes"></a>Códigos de estado de blob  
A tabela seguinte lista os códigos de estado de processamento de um blob.  
  
|Código de estado|Descrição|  
|-----------------|-----------------|  
|`Completed`|O blob concluiu o processamento sem erros.|  
|`CompletedWithErrors`|O blob concluiu o processamento de erros em um ou mais intervalos de página ou blocos, os metadados ou propriedades.|  
|`FileNameInvalid`|O nome de ficheiro é inválido.|  
|`FileNameTooLong`|O nome de ficheiro é demasiado longo.|  
|`FileNotFound`|O ficheiro não encontrado.|  
|`FileAccessDenied`|É negado o acesso ao ficheiro.|  
|`BlobRequestFailed`|Falhou o pedido de serviço Blob para aceder a BLOBs.|  
|`BlobRequestForbidden`|O pedido de serviço Blob para aceder a BLOBs é proibido. Isto pode dever-se a chave de conta de armazenamento inválido ou contentor SAS.|  
|`RenameFailed`|Falha ao mudar o nome do blob (para uma tarefa de importação) ou o ficheiro (para uma tarefa de exportação).|  
|`BlobUnexpectedChange`|Tiver ocorrido uma alteração inesperada com o blob (para uma tarefa de exportação).|  
|`LeasePresent`|Há uma concessão presente no blob.|  
|`IOFailed`|Ocorreu uma falha de e/s de disco ou de rede ao processar o blob.|  
|`Failed`|Ocorreu uma falha desconhecida ao processar o blob.|  
  
## <a name="import-disposition-status-codes"></a>Códigos de estado de disposição de importação  
A tabela seguinte lista os códigos de estado para resolver uma disposição de importação.  
  
|Código de estado|Descrição|  
|-----------------|-----------------|  
|`Created`|O blob foi criado.|  
|`Renamed`|O blob foi alterado por disposição de importação de mudança de nome. O `Blob/BlobPath` elemento contém o URI para o blob cujo nome foi alterado.|  
|`Skipped`|O blob foi ignorado por `no-overwrite` importar disposição.|  
|`Overwritten`|O blob substituíram um blob existente por `overwrite` importar disposição.|  
|`Cancelled`|Uma falha anterior foi parado o processamento a disposição de importação.|  
  
## <a name="page-rangeblock-status-codes"></a>Códigos de estado de intervalo/bloco de página  
A tabela seguinte lista os códigos de estado de processamento de um intervalo de página ou um bloco.  
  
|Código de estado|Descrição|  
|-----------------|-----------------|  
|`Completed`|O intervalo de páginas ou bloco concluiu o processamento sem erros.|  
|`Committed`|O bloco tiver sido consolidado, mas não no bloco de completo lista porque outros blocos tem falha ou coloque a lista de bloqueios completa próprio tem falhou.|  
|`Uncommitted`|O bloco é carregado, mas não consolidado.|  
|`Corrupted`|O intervalo de páginas ou bloco está danificado (o conteúdo não corresponde ao respetivo hash).|  
|`FileUnexpectedEnd`|Foi detetado um fim inesperado do ficheiro.|  
|`BlobUnexpectedEnd`|Foi detetado um fim inesperado do blob.|  
|`BlobRequestFailed`|Falhou o pedido de serviço Blob para aceder ao intervalo de página ou o bloco.|  
|`IOFailed`|Ocorreu uma falha de e/s de disco ou de rede ao processar o intervalo de página ou o bloco.|  
|`Failed`|Ocorreu uma falha desconhecida ao processar o intervalo de página ou o bloco.|  
|`Cancelled`|Uma falha anterior foi parado o processamento adicional do bloco ou intervalo de páginas.|  
  
## <a name="metadata-status-codes"></a>Códigos de estado de metadados  
A tabela seguinte lista os códigos de estado de processamento de metadados do blob.  
  
|Código de estado|Descrição|  
|-----------------|-----------------|  
|`Completed`|Os metadados concluiu o processamento sem erros.|  
|`FileNameInvalid`|O nome de ficheiro de metadados é inválido.|  
|`FileNameTooLong`|O nome de ficheiro de metadados é demasiado longo.|  
|`FileNotFound`|O ficheiro de metadados não foi encontrado.|  
|`FileAccessDenied`|É negado o acesso ao ficheiro de metadados.|  
|`Corrupted`|O ficheiro de metadados está danificado (o conteúdo não corresponde ao respetivo hash).|  
|`XmlReadFailed`|O conteúdo de metadados não é compatível com o formato requerido.|  
|`XmlWriteFailed`|Escrever os metadados de XML falhou.|  
|`BlobRequestFailed`|Falhou o pedido de serviço Blob para aceder aos metadados.|  
|`IOFailed`|Ocorreu uma falha de e/s de disco ou de rede ao processar os metadados.|  
|`Failed`|Ocorreu uma falha desconhecida ao processar os metadados.|  
|`Cancelled`|Uma falha anterior foi parado o processamento adicional dos metadados.|  
  
## <a name="properties-status-codes"></a>Códigos de estado de propriedades  
A tabela seguinte lista os códigos de estado de processamento de propriedades de blob.  
  
|Código de estado|Descrição|  
|-----------------|-----------------|  
|`Completed`|As propriedades tem concluído o processamento sem erros.|  
|`FileNameInvalid`|O nome de ficheiro de propriedades é inválido.|  
|`FileNameTooLong`|O nome de ficheiro de propriedades é demasiado longo.|  
|`FileNotFound`|O ficheiro de propriedades não foi encontrado.|  
|`FileAccessDenied`|É negado o acesso ao ficheiro de propriedades.|  
|`Corrupted`|O ficheiro de propriedades está corrompido (o conteúdo não corresponde ao respetivo hash).|  
|`XmlReadFailed`|O conteúdo de propriedades não é compatível com o formato requerido.|  
|`XmlWriteFailed`|Escrever as propriedades de XML falhou.|  
|`BlobRequestFailed`|Falhou o pedido de serviço Blob para aceder às propriedades.|  
|`IOFailed`|Ocorreu uma falha de e/s de disco ou de rede ao processar as propriedades.|  
|`Failed`|Ocorreu uma falha desconhecida ao processar as propriedades.|  
|`Cancelled`|Uma falha anterior foi parado o processamento adicional das propriedades.|  
  
## <a name="sample-logs"></a>Registos de exemplo  
Segue-se um exemplo de registo verboso.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
O registo de erros correspondente é mostrado abaixo.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 O registo de erros de seguir para uma tarefa de importação contém um erro sobre um ficheiro não encontrado na unidade de importação. Tenha em atenção que o estado dos componentes subsequentes `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

O seguinte registo de erros para uma tarefa de exportação indica que o conteúdo de blob foi escrito com êxito para a unidade, mas que ocorreu um erro ao exportar as propriedades do blob.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Passos seguintes
 
* [API de REST do Storage para importar/exportar](/rest/api/storageimportexport/)
