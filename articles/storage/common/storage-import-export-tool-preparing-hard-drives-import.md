---
title: "Preparar os discos rígidos de uma tarefa de importação do Azure para importar/exportar | Microsoft Docs"
description: "Saiba como preparar os discos rígidos com a ferramenta de WAImportExport para criar uma tarefa de importação para o serviço importar/exportar do Azure."
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
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 2854822907e818297c8d2f74cab48b0afa0d646c
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparar os discos rígidos de uma tarefa de importação

A ferramenta de WAImportExport é a ferramenta de preparação e de reparação de unidade que pode utilizar com o [serviço de importação/exportação do Microsoft Azure](../storage-import-export-service.md). Pode utilizar esta ferramenta para copiar dados para as unidades de disco rígidas que vai são enviados para um datacenter do Azure. Depois de uma tarefa de importação foi concluída, pode utilizar esta ferramenta para reparar blobs que foram corrompidos, foram em falta ou em conflito com outros blobs. Depois de receber as unidades de uma tarefa de exportação foi concluída, pode utilizar esta ferramenta para reparar todos os ficheiros que foram danificado ou em falta nas unidades. Neste artigo, vamos abordar a utilização desta ferramenta.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="requirements-for-waimportexportexe"></a>Requisitos para WAImportExport.exe

- **Configuração da máquina**
  - Windows 7, Windows Server 2008 R2 ou um sistema de operativo mais recente do Windows
  - .NET framework 4 tem de estar instalado. Consulte [FAQ](#faq) no como verificar o .net Framework estiver instalado no computador.
- **Chave de conta de armazenamento** -precisa de, pelo menos, uma das chaves de conta para a conta de armazenamento.

### <a name="preparing-disk-for-import-job"></a>Preparar o disco para a tarefa de importação

- **BitLocker -** BitLocker tem de estar ativado no computador que executa a ferramenta de WAImportExport. Consulte o [FAQ](#faq) como ativar o BitLocker.
- **Discos** acessível a partir do computador no qual a ferramenta de WAImportExport é executada. Consulte [FAQ](#faq) para a especificação de disco.
- **Ficheiros de origem** -os ficheiros que pretende importar tem de ser acessíveis a partir da máquina de cópia, quer estejam na partilha de rede ou uma unidade de disco rígida local.

### <a name="repairing-a-partially-failed-import-job"></a>Reparação de uma tarefa de importação parcialmente falhada

- **Ficheiro de registo de cópia** que é gerado quando o serviço importar/exportar do Azure copia dados entre a conta de armazenamento e de disco. Está localizado na sua conta de armazenamento de destino.

### <a name="repairing-a-partially-failed-export-job"></a>Reparação de uma tarefa de exportação parcialmente falhada

- **Ficheiro de registo de cópia** que é gerado quando o serviço importar/exportar do Azure copia dados entre a conta de armazenamento e de disco. Está localizado na sua conta de armazenamento de origem.
- **Ficheiro de manifesto** -Located [opcional] no disco exportado que foi devolvido pela Microsoft.

## <a name="download-and-install-waimportexport"></a>Transfira e instale WAImportExport

Transferir o [versão mais recente do WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Extraia o conteúdo zipped para um diretório no seu computador.

A tarefa seguinte consiste em criar ficheiros CSV.

## <a name="prepare-the-dataset-csv-file"></a>Preparar o ficheiro CSV de conjunto de dados

### <a name="what-is-dataset-csv"></a>O que é o conjunto de dados CSV

Um ficheiro CSV de conjunto de dados é que o valor do sinalizador /dataset é um ficheiro CSV que contém uma lista de diretórios e/ou uma lista de ficheiros copiados para unidades de destino. O primeiro passo para criar uma tarefa de importação consiste em determinar os diretórios e ficheiros, se pretender importar. Isto pode ser uma lista de diretórios, uma lista dos ficheiros exclusivos ou uma combinação desses dois. Quando um diretório estiver incluído, todos os ficheiros no diretório e nos seus subdiretórios farão parte da tarefa de importação.

Para cada diretório ou ficheiro a ser importado, tem de identificar um diretório virtual de destino ou um blob no serviço Blob do Azure. Irá utilizar nestes destinos como entradas para a ferramenta de WAImportExport. Diretórios devem ser delimitados com o caráter de barra "/".

A tabela seguinte mostra alguns exemplos de destinos de blob:

| Ficheiro de origem ou diretório | Blob de destino ou diretório virtual |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.Core.Windows.NET/Video |
| H:\Photo | https://mystorageaccount.blob.Core.Windows.NET/Photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.Core.Windows.NET/Favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.Core.Windows.NET/Music |

### <a name="sample-datasetcsv"></a>Dataset.csv de exemplo

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Campos de ficheiro CSV de conjunto de dados

| Campo | Descrição |
| --- | --- |
| BasePath | **[Necessário]**<br/>O valor deste parâmetro representa a origem de onde estão localizados a dados a serem importados. A ferramenta irá recursivamente copiar todos os dados localizados neste caminho.<br><br/>**Valores permitidos**: este tem de ser um caminho válido no computador local ou um caminho de partilha válido e deve estar acessível ao utilizador. O caminho do diretório tem de ser um caminho absoluto (não um caminho relativo). Se o caminho termina com "\\", representa um diretório pessoa um caminho final sem"\\" representa um ficheiro.<br/>Não existem regex é permitido neste campo. Se o caminho contiver espaços, coloque-o "".<br><br/>**Exemplo**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Necessário]**<br/> O caminho para o diretório virtual de destino na sua conta de armazenamento do Windows Azure. O diretório virtual poderá ou poderá já existe. Se não existir, irá criar um serviço de importação/exportação.<br/><br/>Não se esqueça de utilizar os nomes dos contentores válidos quando especificar diretórios virtuais de destino ou blobs. Tenha em atenção que os nomes dos contentores têm de estar em minúsculas. Para regras de nomenclatura de contentor, consulte [nomenclatura e referência de contentores, Blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Se apenas raiz for especificada, a estrutura do diretório de origem é replicada no contentor do blob de destino. Se pretender uma estrutura de diretório diferente na origem, várias linhas de mapeamento de CSV<br/><br/>Pode especificar um contentor ou um prefixo de blob, como leitores de música/70s /. O diretório de destino tem de começar com o nome do contentor, seguido por uma barra "/" e, opcionalmente, pode incluir um diretório virtual blob que termina com "/".<br/><br/>Quando o contentor de destino é o contentor de raiz, tem de especificar explicitamente o recipiente-raiz, incluindo a barra, como $root /. Uma vez que os blobs no contentor de raiz não podem incluir "/" nos respetivos nomes, não serão copiados todos os subdiretórios no diretório de origem quando o diretório de destino é o contentor de raiz.<br/><br/>**Exemplo**<br/>Se o caminho de blob de destino for https://mystorageaccount.blob.core.windows.net/video, o valor deste campo pode ser vídeo /  |
| BlobType | **[Opcional]**  Bloco &#124; página<br/>Atualmente, o serviço de importação/exportação suporta 2 tipos de Blobs. Página os blobs e bloco BlobsBy predefinição em todos os ficheiros serão importados como Blobs de blocos. E \*. vhd e \*importado. vhdx como BlobsThere de página é um limite no tamanho permitido de BLOBs de páginas e BLOBs de blocos. Consulte [metas de escalabilidade do armazenamento](storage-scalability-targets.md) para obter mais informações.  |
| Disposição | **[Opcional]**  mudar o nome de &#124; substituir o não &#124; substituir <br/> Este campo especifica o comportamento de cópia durante a importação revertidos Quando estão a ser carregados dados para a conta de armazenamento do disco. Opções disponíveis são: mudar o nome &#124; Substituir &#124; não de substituição. Por predefinição "mudar o nome" se nada especificado. <br/><br/>**Mudar o nome**: Se estiver presente um objeto com o mesmo nome, cria uma cópia no destino.<br/>Substituir: substitui o ficheiro com o ficheiro mais recente. O ficheiro com o wins last-modified.<br/>**Substituir o não**: ignora a escrever o ficheiro se já está presente.|
| MetadataFile | **[Opcional]** <br/>O valor para este campo é o ficheiro de metadados que pode ser fornecido se o tem de manter os metadados dos objetos ou forneça metadados personalizados. Caminho para o ficheiro de metadados para os blobs de destino. Consulte [metadados e o formato de ficheiro de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações |
| PropertiesFile | **[Opcional]** <br/>Caminho para o ficheiro de propriedade para os blobs de destino. Consulte [metadados e o formato de ficheiro de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Preparar ficheiro InitialDriveSet ou AdditionalDriveSet CSV

### <a name="what-is-driveset-csv"></a>O que é driveset CSV

O valor do sinalizador /InitialDriveSet ou /AdditionalDriveSet é um ficheiro CSV que contém a lista de discos nos quais as letras de unidade estão mapeadas para que a ferramenta corretamente pode escolher a lista de discos para estar preparado. Se o tamanho de dados for superior a um tamanho de disco individual, a ferramenta de WAImportExport será distribua os dados entre múltiplos discos inscritas neste ficheiro CSV de uma forma otimizada.

Não há nenhum limite no número de discos que de dados podem ser escritos numa única sessão. A ferramenta irá distribuir dados com base no tamanho do disco e o tamanho da pasta. Irá selecionar o disco que é a maioria das otimizada para o tamanho do objeto. Os dados quando carregado para a conta do storage serão convergidos para a estrutura de diretórios que foi especificada no ficheiro de conjunto de dados. Para criar um driveset CSV, siga os passos abaixo.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Criar o volume básico e atribua a letra de unidade

Para criar um volume básico e atribuir uma letra de unidade, ao seguir as instruções para "Criação de partições mais simples" fornecido durante a [descrição geral da gestão de discos](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Ficheiro de exemplo InitialDriveSet e AdditionalDriveSet CSV

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Campos dos ficheiros Driveset CSV

| Campos | Valor |
| --- | --- |
| Letradaunidade | **[Necessário]**<br/> Cada unidade que está a ser fornecida para a ferramenta como o destino tem de ter um volume NTFS simples no mesmo e uma letra de unidade atribuída.<br/> <br/>**Exemplo**: R ou r |
| FormatOption | **[Necessário]**  Formato &#124; AlreadyFormatted<br/><br/> **Formato**: especificar Isto irá formatar todos os dados no disco. <br/>**AlreadyFormatted**: A ferramenta irá ignorar a formatação quando este valor for especificado. |
| SilentOrPromptOnFormat | **[Necessário]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: fornecer este valor irá permitir que o utilizador executar a ferramenta no modo silencioso. <br/>**PromptOnFormat**: A ferramenta pedirá ao utilizador para confirmar se a ação realmente destina-se no formato de cada.<br/><br/>Se não definir comando irá abortar e apresentar a mensagem de erro: "Valor incorreto para SilentOrPromptOnFormat: none" |
| Encriptação | **[Necessário]**  Encriptar &#124; AlreadyEncrypted<br/> O valor deste campo decide que disco para encriptar e que não a. <br/><br/>**Encriptar**: ferramenta irá formatar o disco. Se o valor do campo "FormatOption" for "Format", em seguida, este valor tem de ser "Encriptar". Se "AlreadyEncrypted" é especificado neste caso, irá resultar num erro "Quando é especificado o formato, encriptar deve ser também especificado".<br/>**AlreadyEncrypted**: ferramenta será desencriptar a unidade com BitLockerKey fornecido no campo "ExistingBitLockerKey". Se o valor do campo "FormatOption" for "AlreadyFormatted", em seguida, este valor pode ser "Encriptar" ou "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Necessário]**  Se o valor do campo "Encriptação" for "AlreadyEncrypted"<br/> O valor deste campo é a chave do BitLocker que estão associada com o disco específico. <br/><br/>Este campo deve ser deixado em branco se o valor do campo "Encriptação" for "Encriptar".  Se a chave do BitLocker é especificado neste caso, irá resultar num erro "Chave do Bitlocker não deve ser especificado".<br/>  **Exemplo**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Preparar o disco para a tarefa de importação

Para preparar unidades para uma tarefa de importação, chame a ferramenta de WAImportExport com o **PrepImport** comando. Os parâmetros que incluir depende se esta é a primeira sessão de cópia ou uma sessão de cópia subsequentes.

### <a name="first-session"></a>Primeira sessão

Primeira sessão de cópia para copiar um diretório de Single/Multiple para uma ferramenta de disco (consoante o que é especificado no ficheiro CSV) WAImportExport único/vários PrepImport comandos para a primeira sessão de cópia copiar diretórios e/ou os ficheiros com uma nova sessão de cópia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Adicionar dados de sessão subsequentes

Sessões de cópia subsequentes, não terá de especificar os parâmetros iniciais. Terá de utilizar o mesmo ficheiro de diário por ordem para a ferramenta de recordar onde deixado na sessão anterior. O estado da sessão de cópia é escrito no ficheiro de diário de alterações. Segue-se a sintaxe de uma sessão subsequentes de cópia copiar diretórios adicionais e ou ficheiros:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Adicionar unidades à sessão mais recente

Se os dados não foi possível caber numa unidades especificadas no InitialDriveset, um pode utilizar a ferramenta para adicionar unidades adicionais à mesma sessão de cópia. 

>[!NOTE] 
>O id de sessão deve corresponder ao id de sessão anterior. Ficheiros do diário de alterações devem corresponder ao especificado no sessão anterior.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Abortar a sessão mais recente:

Se uma sessão de cópia é interrompida e não é possível retomar (por exemplo, se um diretório de origem foi tentativa inacessível), tem de abortar a sessão atual para que pode ser revertida e podem ser iniciadas novas sessões de cópia:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Apenas a última sessão de cópia, se terminou anormalmente, pode ser abortada. Tenha em atenção que não é possível abortar a primeira sessão de cópia para uma unidade. Em vez disso, tem de reiniciar a sessão de cópia com um novo ficheiro de diário de alterações.

### <a name="resume-a-latest-interrupted-session"></a>Retomar uma sessão interrompida mais recente

Se uma sessão de cópia é interrompida por qualquer motivo, pode retomá-lo ao executar a ferramenta com o ficheiro do diário de alterações especificado:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Quando retomar uma sessão de cópia, não modifique os ficheiros de origem de dados e diretórios adicionando ou removendo ficheiros.

## <a name="waimportexport-parameters"></a>Parâmetros de WAImportExport

| Parâmetros | Descrição |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Necessário**<br/> Caminho para o ficheiro do diário de alterações. Um ficheiro de diário controla um conjunto de unidades e as regista o progresso na preparação destas unidades. O ficheiro do diário de alterações têm sempre de ser especificado.  |
|     /LOGDIR:&lt;LogDirectory&gt;  | **Opcional**. O diretório de registo.<br/> Ficheiros de registo verboso, bem como alguns ficheiros temporários serão escritos neste diretório. Se não for especificado, o atual diretório será utilizado como o diretório de registo. O diretório de registo pode ser especificado apenas uma vez para o mesmo ficheiro de diário de alterações.  |
|     formado:&lt;SessionId&gt;  | **Necessário**<br/> O Id de sessão que é utilizado para identificar uma sessão de cópia. É utilizado para garantir a recuperação exata de uma sessão de cópia interrompida.  |
|     / ResumeSession  | Opcional. Se a última sessão de cópia foi anormalmente terminada, este parâmetro pode ser especificado para retomar a sessão.   |
|     / AbortSession  | Opcional. Se a última sessão de cópia foi anormalmente terminada, este parâmetro pode ser especificado para a sessão de abortar.  |
|     /SN:&lt;StorageAccountName&gt;  | **Necessário**<br/> Só é aplicável para RepairImport e RepairExport. O nome da conta de armazenamento.  |
|     /SK:&lt;StorageAccountKey&gt;  | **Necessário**<br/> A chave da conta de armazenamento. |
|     / InitialDriveSet:&lt;driveset.csv&gt;  | **Necessário** ao executar a primeira sessão de cópia<br/> Um ficheiro CSV que contém uma lista de unidades para preparar.  |
|     / AdditionalDriveSet:&lt;driveset.csv&gt; | **Necessário**. Ao adicionar unidades a atual sessão de cópia. <br/> Um ficheiro CSV que contém uma lista de unidades adicionais a adicionar.  |
|      /r:&lt;RepairFile&gt; | **Necessário** apenas aplicável para RepairImport e RepairExport.<br/> Caminho para o ficheiro para controlar o progresso de reparação. Cada unidade tem de ter um e apenas um ficheiro de reparação.  |
|     /d:&lt;TargetDirectories&gt; | **Necessário**. Só é aplicável para RepairImport e RepairExport. Para RepairImport, um ou mais diretórios separados por ponto e vírgula para reparar; Para RepairExport, um diretório para reparar, por exemplo, raiz do diretório da unidade.  |
|     / CopyLogFile:&lt;DriveCopyLogFile&gt; | **Necessário** apenas aplicável para RepairImport e RepairExport. Caminho para o ficheiro de registo de cópia de unidade (verboso ou de erros).  |
|     / ManifestFile:&lt;DriveManifestFile&gt; | **Necessário** apenas aplicável para RepairExport.<br/> Caminho para o ficheiro de manifesto de unidade.  |
|     / PathMapFile:&lt;DrivePathMapFile&gt; | **Opcional**. Só é aplicável para RepairImport.<br/> Caminho do ficheiro que contém os mapeamentos de caminhos de ficheiro relativo à raiz da unidade para localizações de ficheiros reais (delimitado por separador). Quando especificado pela primeira vez, este será preenchido com caminhos de ficheiros com destinos vazios, que significa não se encontram no TargetDirectories, acesso negado com um nome inválido, ou existam vários diretórios. O ficheiro de mapa de caminho pode ser editado para incluir os caminhos de destino correto e manualmente especificado novamente para a ferramenta resolver corretamente os caminhos de ficheiro.  |
|     / ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Necessário**. Só é aplicável para PreviewExport.<br/> Caminho para o XML do ficheiro que contém lista de caminhos de blob ou blob prefixos de caminho para os blobs ser exportada. O formato de ficheiro é o mesmo que o formato de blob de lista de blob na operação de colocar a tarefa de REST API do serviço de importação/exportação.  |
|     / DriveSize:&lt;DriveSize&gt; | **Necessário**. Só é aplicável para PreviewExport.<br/>  Tamanho das unidades para ser utilizado para exportação. Por exemplo, 500 GB, 1,5 TB. Nota: 1 GB = 1,000,000,000 bytes1 TB = 1,000,000,000,000 bytes  |
|     / Conjunto de dados:&lt;dataset.csv&gt; | **Necessário**<br/> Um ficheiro CSV que contém uma lista de diretórios e/ou uma lista de ficheiros copiados para unidades de destino.  |
|     /silentmode  | **Opcional**.<br/> Se não for especificado, irá relembrá-o requisito de unidades e tem a confirmação para continuar.  |

## <a name="tool-output"></a>Saída de ferramenta

### <a name="sample-drive-manifest-file"></a>Ficheiro de manifesto de unidade de exemplo

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Ficheiro de diário de alterações de exemplo (XML) para cada unidade

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Ficheiro de diário de alterações de exemplo (JRN) para a sessão que regista o registo de sessões

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>FAQ

### <a name="general"></a>Geral

#### <a name="what-is-waimportexport-tool"></a>O que é a ferramenta de WAImportExport?

A ferramenta de WAImportExport é a ferramenta de preparação e de reparação de unidade que pode utilizar com o serviço de importação/exportação do Microsoft Azure. Pode utilizar esta ferramenta para copiar dados para as unidades de disco rígidas que vai são enviados para um centro de dados do Azure. Depois de uma tarefa de importação foi concluída, pode utilizar esta ferramenta para reparar blobs que foram corrompidos, foram em falta ou em conflito com outros blobs. Depois de receber as unidades de uma tarefa de exportação foi concluída, pode utilizar esta ferramenta para reparar todos os ficheiros que foram danificado ou em falta nas unidades.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Como funciona a ferramenta de WAImportExport em múltiplos dir de origem e discos?

Se o tamanho dos dados é superior ao tamanho do disco, a ferramenta de WAImportExport distribuir os dados nos discos de uma forma otimizada. Pode ser feita a cópia de dados para vários discos em paralelo ou sequencialmente. Não há nenhum limite no número de discos que de dados podem ser escritos em simultâneo. A ferramenta irá distribuir dados com base no tamanho do disco e o tamanho da pasta. Irá selecionar o disco que é a maioria das otimizada para o tamanho do objeto. Os dados quando carregado para a conta do storage serão convergidos para a estrutura de diretório especificado.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Onde posso encontrar a versão anterior da ferramenta de WAImportExport?

A ferramenta de WAImportExport tem todas as funcionalidades que tinha ferramenta WAImportExport V1. Ferramenta de WAImportExport permite aos utilizadores especificar várias origens e escrever em várias unidades. Além disso, um pode gerir facilmente várias localizações de origem a partir da qual os dados têm de ser copiados num único ficheiro CSV. No entanto, no caso precisa de suporte SAS ou pretende copiar única origem para um único disco, que pode [Transferir WAImportExport V1 ferramenta] (http://go.microsoft.com/fwlink/? LinkID = 301900&amp;clcid = 0x409) e consulte [WAImportExport V1 referência](storage-import-export-tool-how-to-v1.md) para obter ajuda com a utilização de WAImportExport V1.

#### <a name="what-is-a-session-id"></a>O que é um ID de sessão?

A ferramenta de espera que a sessão de cópia (/ id) parâmetro seja o mesmo se a intenção for distribuir os dados entre vários discos. Manter o mesmo nome da sessão de cópia irá ativar o utilizador copiar dados de uma ou várias localizações de origem para um ou vários discos/diretórios de destino. Manter o mesmo id de sessão permite que a ferramenta recolher a cópia de ficheiros a partir de onde se foi deixada a última vez.

No entanto, a mesma sessão de cópia não pode ser utilizado para importar dados para contas de armazenamento diferente.

Quando o nome de cópia de sessão é a mesmo em várias execuções da ferramenta, o ficheiro de registo (/ logdir) e a chave de conta de armazenamento (/ sk) também deve ser o mesmo.

SessionId pode ser composto por letras, 0 ~ understore 9, (\_), travessão (-) ou hash (#), e o seu comprimento deve ser 3 ~ 30.

Por exemplo, 1 de sessão ou sessão #1 ou sessão\_1

#### <a name="what-is-a-journal-file"></a>O que é um ficheiro do diário de alterações?

Sempre que executar a ferramenta de WAImportExport para copiar ficheiros para o disco rígido, a ferramenta cria uma sessão de cópia. O estado da sessão de cópia é escrito no ficheiro de diário de alterações. Se uma sessão de cópia é interrompida (por exemplo, devido a uma perda de energia do sistema), é possível retomar executar novamente a ferramenta e especificar o ficheiro de diário de alterações na linha de comandos.

Para cada unidade de disco rígida a preparar-se com a ferramenta de importação/exportação do Azure, a ferramenta irá criar um ficheiro de diário único com o nome "&lt;DriveID&gt;. xml" em que o Id de unidade é o número de série associado para a unidade que a ferramenta lê a partir do disco. Terá dos ficheiros do diário de alterações em todos os das suas unidades ao criar a tarefa de importação. O ficheiro de diário também pode ser utilizado para retomar a preparação de unidade, se a ferramenta é interrompida.

#### <a name="what-is-a-log-directory"></a>O que é um diretório de registo?

O diretório de registo Especifica um diretório a ser utilizado para armazenar os registos verbosos, bem como os ficheiros de manifesto temporários. Se não for especificado, será utilizado como o diretório de registo do diretório atual. Os registos são registos verbosos.

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="what-are-the-specifications-of-my-disk"></a>Quais são as especificações do meu disco?

Um ou mais vazias 2.5 polegadas ou 3.5 polegadas SATAII ou III ou SSD unidades de disco rígido ligadas à máquina cópia.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Como ativar o BitLocker no meu computador?

Uma forma Simple de verificar é clicando na unidade do sistema. Irá mostrar opções para o Bitlocker se estiver ativada a capacidade. Se estiver desligado, não irá vê-lo.

![Verifique o BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Eis um artigo no [como ativar o BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

É possível que a máquina não tem TPM chip. Se não obtiver uma saída utilizando tpm.msc, observe as FAQ seguintes.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Como desativar Trusted Platform Module (TPM) no BitLocker?
> [!NOTE]
> Apenas se não existir nenhum TPM nos respetivos servidores, terá de desativar a política TPM. Não é necessário desativar TPM, se existir um TPM fidedigno no servidor do utilizador. 
> 

Para desativar o TPM no BitLocker, efetuar os seguintes passos:<br/>
1. Iniciar **Editor de políticas de grupo** escrevendo gpedit.msc numa linha de comandos. Se **Editor de políticas de grupo** parece estar disponível para ativar o BitLocker primeiro. Consulte as FAQ anterior.
2. Abra **política de computador Local &gt; configuração do computador &gt; modelos administrativos &gt; componentes do Windows&gt; encriptação de unidade BitLocker &gt; deunidadesdesistemaoperativo**.
3. Editar **requer autenticação adicional durante o arranque** política.
4. Defina a política para **ativado** e certifique-se **permitir BitLocker sem um TPM compatível** está marcada.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Como verificar se o .NET 4 ou versão posterior está instalada no meu computador?

Todas as versões do Microsoft .NET Framework estão instaladas no seguinte diretório: %windir%\Microsoft.NET\Framework\

Navegue para a parte acima mencionada no seu computador de destino onde a ferramenta tem de executar. Procure o nome da pasta começados por "v4". Ausência de um diretório tal significa que .NET 4 não está instalado no seu computador. Pode transferir o .net 4 na sua máquina utilizando [o Microsoft .NET Framework 4 (programa de instalação Web)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Limites

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>O número de unidades pode posso preparar/envio ao mesmo tempo?

Não há nenhum limite no número de discos que pode preparar a ferramenta. No entanto, a ferramenta espera letras de unidade como entradas. Que limita a preparação de disco em simultâneo 25. Uma única tarefa pode processar o máximo de 10 discos cada vez. Se a preparar-se mais de 10 discos direcionada para a mesma conta de armazenamento, os discos podem ser distribuídos por várias tarefas.

#### <a name="can-i-target-more-than-one-storage-account"></a>Pode visar mais do que uma conta de armazenamento?

Apenas uma conta de armazenamento pode ser submetida por tarefa e na sessão de cópia única.

### <a name="capabilities"></a>Capacidades

#### <a name="does-waimportexportexe-encrypt-my-data"></a>WAImportExport.exe encriptar os meus dados?

Sim. A encriptação BitLocker está ativada e necessárias para este processo.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>O que será a hierarquia dos meus dados quando é apresentado na conta de armazenamento?

Apesar de dados são distribuídos entre discos, os dados quando carregado para a conta do storage serão convergidos de volta para a estrutura de diretório especificada no ficheiro CSV de conjunto de dados.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Discos quantos da entrada terão Active Directory e/s em paralelo, quando copiar está em curso?

A ferramenta distribui dados de entrada discos com base no tamanho dos ficheiros de entrada. Dito isto, o número de ativos discos em paralelo delends completamente na natureza dos dados de entrada. Dependendo do tamanho dos ficheiros individuais no conjunto de dados de entrada, um ou mais discos podem mostrar o Active Directory e/s em paralelo. Consulte a questão seguinte para obter mais detalhes.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Como a ferramenta de distribuir os ficheiros nos discos?

Ferramenta WAImportExport lê e escreve os ficheiros batch por lote, um batch contém o número máximo de 100000 ficheiros. Isto significa que os 100000 ficheiros máx. podem ser escritos paralelo. Vários discos são escritos em simultâneo para se estes 100000 ficheiros são distribuídos por várias unidades. No entanto, se a ferramenta escreve para vários discos em simultâneo ou um único disco depende do tamanho cumulativo do lote. Por exemplo, em caso de ficheiros mais pequenos, se todos os ficheiros 10,0000 são capazes caber numa única unidade, ferramenta irá escrever um disco apenas durante o processamento deste lote.

### <a name="waimportexport-output"></a>Saída de WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Existem dois ficheiros do diário de alterações, que se deverá carregar para o portal do Azure?

**. XML** -para cada unidade de disco rígida deve preparar-se com a ferramenta de WAImportExport, a ferramenta irá criar um ficheiro de diário único com o nome `<DriveID>.xml` onde DriveID é o número de série associado para a unidade que a ferramenta lê a partir do disco. Terá dos ficheiros do diário de alterações em todos os das suas unidades ao criar a tarefa de importação no portal do Azure. Este ficheiro de diário também pode ser utilizado para retomar a preparação de unidade, se a ferramenta é interrompida.

**.jrn** -o ficheiro de diário de alterações com o sufixo `.jrn` contém o estado de todas as sessões de cópia para uma unidade de disco rígida. Também contém as informações necessárias para criar a tarefa de importação. Sempre tem de especificar um ficheiro do diário de alterações ao executar a ferramenta de WAImportExport, bem como um ID de sessão de cópia.

## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup.md)
* [Definir propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import.md)
* [Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Referência rápida para comandos utilizados com frequência](storage-import-export-tool-quick-reference.md) 
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparação de uma tarefa de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
