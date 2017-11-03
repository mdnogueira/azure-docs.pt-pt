---
title: "Preparar os discos rígidos de uma tarefa de importação do Azure para importar/exportar - v1 | Microsoft Docs"
description: "Saiba como preparar os discos rígidos utilizando a ferramenta de v1 WAImportExport para criar uma tarefa de importação para o serviço importar/exportar do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 3d818245-8b1b-4435-a41f-8e5ec1f194b2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 361e16262e528c7dea1bab4b9d945a28af8be399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparar as unidades de disco rígido para uma tarefa de importação
Para preparar um ou mais unidades de disco rígido para uma tarefa de importação, siga estes passos:

-   Identificar os dados para importar para o serviço Blob

-   Identificar os diretórios virtuais do destino e os blobs no serviço Blob

-   Determinar quantos unidades, terá de

-   Copiar os dados para cada uma das suas unidades de disco rígido

 Para um fluxo de trabalho de exemplo, consulte [fluxo de trabalho de exemplo para preparar os discos rígidos de uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identificar os dados a importar
 O primeiro passo para criar uma tarefa de importação consiste em determinar os diretórios e ficheiros, se pretender importar. Isto pode ser uma lista de diretórios, uma lista dos ficheiros exclusivos ou uma combinação desses dois. Quando um diretório estiver incluído, todos os ficheiros no diretório e nos seus subdiretórios farão parte da tarefa de importação.

> [!NOTE]
>  Uma vez que subdiretórios recursivamente incluída quando um diretório principal está incluído, especifique apenas o diretório principal. Não também especificar qualquer um dos seus subdiretórios.
>
>  Atualmente, a ferramenta de importação/exportação do Microsoft Azure tem a seguinte limitação: se um diretório contém mais dados do que pode conter um disco rígido, em seguida, o diretório tem de ser dividido em diretórios mais pequenos. Por exemplo, se um diretório contém 2,5 TB de dados e a capacidade do disco rígido é de apenas 2TB, em seguida, terá de quebrar o diretório de 2,5 TB diretórios mais pequenos. Esta limitação será resolvida numa versão posterior da ferramenta.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Identificar as localizações de destino no serviço blob
 Para cada diretório ou o ficheiro que será importado, tem de identificar um diretório virtual de destino ou um blob no serviço Blob do Azure. Irá utilizar nestes destinos como entradas para a ferramenta de importação/exportação do Azure. Tenha em atenção que os diretórios devem ser delimitados com o caráter de barra "/".

 A tabela seguinte mostra alguns exemplos de destinos de blob:

|Ficheiro de origem ou diretório|Blob de destino ou diretório virtual|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.Core.Windows.NET/Video|
|H:\Photo|https://mystorageaccount.blob.Core.Windows.NET/Photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.blob.Core.Windows.NET/Favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.blob.Core.Windows.NET/Music|

## <a name="determine-how-many-drives-are-needed"></a>Determinar quantos unidades são necessárias
 Em seguida, terá de determinar:

-   O número de unidades de disco rígido necessário para armazenar os dados.

-   Os diretórios e/ou os ficheiros de autónomo que serão copiados para cada disco rígido.

 Certifique-se de que tem o número de unidades de disco rígido, terá de armazenar os dados que estiver a transferir.

## <a name="copy-data-to-your-hard-drive"></a>Copiar dados para o disco rígido
 Esta secção descreve como chamar a ferramenta de importação/exportação do Azure para copiar os dados para um ou mais unidades de disco rígido. Sempre que chamar a ferramenta de importação/exportação do Azure, criar um novo *copiar sessão*. Criar sessão de cópia, pelo menos, um para cada unidade em que copiar dados; em alguns casos, poderá necessitar de mais do que uma sessão de cópia para copiar todos os dados única unidade. Seguem-se algumas razões que poderá ter várias sessões de cópia:

-   Tem de criar uma sessão de cópia separada para cada unidade que copiar para.

-   Uma sessão de cópia pode copiar um único diretório ou um blob único para a unidade. Se estiver a copiar vários diretórios, vários blobs ou uma combinação de ambos, terá de criar várias sessões de cópia.

-   Pode especificar propriedades e os metadados que serão definidos em blobs importados como parte de uma tarefa de importação. As propriedades ou metadados que especificou para uma sessão de cópia serão aplicada a todos os blobs especificados por essa sessão de cópia. Se pretender especificar propriedades diferentes ou metadados para alguns blobs, terá de criar uma sessão de cópia separada. Consulte [metadados durante o processo de importação e propriedades da definição](storage-import-export-tool-setting-properties-metadata-import-v1.md)para obter mais informações.

> [!NOTE]
>  Se tiver várias máquinas que cumprem os requisitos descritos na [definição se a ferramenta do Azure para importar/exportar](storage-import-export-tool-setup-v1.md), pode copiar dados para várias unidades de disco rígido em paralelo, executando uma instância desta ferramenta em cada máquina.

 Para cada unidade de disco rígida a preparar-se com a ferramenta de importação/exportação do Azure, a ferramenta irá criar um ficheiro único diário de alterações. Terá dos ficheiros do diário de alterações em todos os das suas unidades ao criar a tarefa de importação. O ficheiro de diário também pode ser utilizado para retomar a preparação de unidade, se a ferramenta é interrompida.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Sintaxe da ferramenta de importação/exportação do Azure para uma tarefa de importação
 Para preparar unidades para uma tarefa de importação, chame a ferramenta de importação/exportação do Azure com o **PrepImport** comando. Os parâmetros que incluir depende se esta é a primeira sessão de cópia ou uma sessão de cópia subsequentes.

 A primeira sessão de cópia para uma unidade requer alguns parâmetros adicionais para especificar a chave de conta de armazenamento; a letra de unidade de destino; Indica se a unidade tem de ser formatada; Se a unidade tem de estar encriptada e, se assim for, a chave do BitLocker; e o diretório de registo. Segue-se a sintaxe para uma sessão de cópia inicial copiar um diretório ou um único ficheiro:

 **Primeiro de copiar sessão para copiar um único diretório**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Primeiro de copiar sessão copiar um ficheiro único**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 Sessões de cópia subsequentes, não terá de especificar os parâmetros iniciais. Segue-se a sintaxe de uma sessão subsequentes de cópia copiar um diretório ou um único ficheiro:

 **Sessões subsequentes cópia para copiar um único diretório**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Sessões subsequentes cópia para copiar um ficheiro único**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parâmetros para a primeira sessão de cópia para uma unidade de disco rígida
 Sempre que executar a ferramenta de importação/exportação do Azure para copiar ficheiros para o disco rígido, a ferramenta cria uma sessão de cópia. Cada sessão de cópia copia um único diretório ou um único ficheiro para um disco rígido. O estado da sessão de cópia é escrito no ficheiro de diário de alterações. Se uma sessão de cópia é interrompida (por exemplo, devido a uma perda de energia do sistema), é possível retomar executar novamente a ferramenta e especificar o ficheiro de diário de alterações na linha de comandos.

> [!WARNING]
>  Se especificar o **/Formatar** parâmetro para a primeira sessão de cópia, a unidade será formatada e todos os dados na unidade serão eliminados. Recomenda-se a utilização de pens em branco apenas para a sua sessão de cópia.

 O comando utilizado para a primeira sessão de cópia para cada unidade requer diferentes parâmetros que os comandos para sessões de cópia subsequentes. A tabela seguinte lista os parâmetros adicionais que estão disponíveis para a primeira sessão de cópia:

|Parâmetro da linha de comandos|Descrição|
|-----------------------------|-----------------|
|**/SK:**< StorageAccountKey\>|`Optional.`A chave de conta de armazenamento para a conta de armazenamento para o qual os dados serão importados. Tem de incluir um **/sk:**< StorageAccountKey\> ou **/csas:**< ContainerSas\> no comando.|
|**/csas:**< ContainerSas\>|`Optional`. O contentor de SAS a utilizar para importar dados para a conta de armazenamento. Tem de incluir um **/sk:**< StorageAccountKey\> ou **/csas:**< ContainerSas\> no comando.<br /><br /> O valor para este parâmetro tem de começar com o nome do contentor, seguido de um ponto de interrogação (?) e o token SAS. Por exemplo:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> As permissões, se especificado no URL ou numa política de acesso armazenada, tem de incluir leitura, escrita e eliminação para tarefas de importação e leitura, escrita e lista para as tarefas de exportação.<br /><br /> Quando este parâmetro for especificado, todos os blobs para ser importado ou exportado tem de estar dentro do contentor especificado na assinatura de acesso partilhado.|
|**/t:**< TargetDriveLetter\>|`Required.`A letra de unidade de disco rígido destino para a sessão de cópia atual, sem a vírgula decimal.|
|**/Format**|`Optional.`Especificar este parâmetro, quando a unidade tem de ser formatado; caso contrário, omita-lo. Antes da ferramenta formata a unidade, irá solicitar uma confirmação da consola. Para suprimir a confirmação, especifique o parâmetro /silentmode.|
|**/silentmode**|`Optional.`Especificar este parâmetro para suprimir a confirmação para a unidade de targert de formatação.|
|**/ encriptar**|`Optional.`Especificar este parâmetro quando a unidade ainda não foi encriptada com BitLocker e tem de ser encriptados pela ferramenta. Se a unidade já foi encriptada com BitLocker, em seguida, omita este parâmetro e especifique o `/bk` parâmetro, fornecendo a chave do BitLocker existente.<br /><br /> Se especificar o `/format` parâmetro, em seguida, que também tem de especificar o `/encrypt` parâmetro.|
|**/BK:**< BitLockerKey\>|`Optional.`Se `/encrypt` é especificado, omita este parâmetro. Se `/encrypt` for omitido, é necessário ter já encriptou a unidade com o BitLocker. Utilize este parâmetro para especificar a chave do BitLocker. A encriptação BitLocker é necessária para todos os discos rígidos para tarefas de importação.|
|**/LOGDIR:**< LogDirectory\>|`Optional.`O diretório de registo Especifica um diretório a ser utilizado para armazenar os registos verbosos, bem como os ficheiros de manifesto temporários. Se não for especificado, será utilizado como o diretório de registo do diretório atual.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parâmetros necessários para todas as sessões de cópia
 O ficheiro do diário de alterações contém o estado de todas as sessões de cópia para uma unidade de disco rígida. Também contém as informações necessárias para criar a tarefa de importação. Tem de especificar sempre um ficheiro do diário de alterações ao executar a ferramenta de importação/exportação do Azure, bem como um ID de sessão de cópia:

|||
|-|-|
|Parâmetro de linha de comandos|Descrição|
|**/j:**< JournalFile\>|`Required.`O caminho para o ficheiro do diário de alterações. Cada unidade tem de ter exatamente um ficheiro de diário de alterações. Tenha em atenção que o ficheiro do diário de alterações não têm de residir na unidade de destino. A extensão de ficheiro do diário de alterações é `.jrn`.|
|**formado:**< SessionId\>|`Required.`O ID de sessão identifica uma sessão de cópia. É utilizado para garantir a recuperação exata de uma sessão de cópia interrompida. Os ficheiros que são copiados numa sessão de cópia são armazenados num diretório com o nome depois do ID de sessão na unidade de destino.|

### <a name="parameters-for-copying-a-single-directory"></a>Parâmetros para copiar um único diretório
 Quando copiar um único diretório, aplicam os seguintes parâmetros obrigatórios e opcionais:

|Parâmetro de linha de comandos|Descrição|
|----------------------------|-----------------|
|**/srcdir:**< SourceDirectory\>|`Required.`O diretório de origem que contém os ficheiros a ser copiado para a unidade de destino. O caminho do diretório tem de ser um caminho absoluto (não um caminho relativo).|
|**/dstdir:**< DestinationBlobVirtualDirectory\>|`Required.`O caminho para o diretório virtual de destino na sua conta de armazenamento do Windows Azure. O diretório virtual poderá ou poderá já existe.<br /><br /> Pode especificar um contentor ou como um prefixo de blob `music/70s/`. O diretório de destino tem de começar com o nome do contentor, seguido por uma barra "/" e, opcionalmente, pode incluir um diretório virtual blob que termina com "/".<br /><br /> Quando o contentor de destino é o contentor de raiz, tem de especificar explicitamente o recipiente-raiz, incluindo a barra, como `$root/`. Uma vez que os blobs no contentor de raiz não podem incluir "/" nos respetivos nomes, não serão copiados todos os subdiretórios no diretório de origem quando o diretório de destino é o contentor de raiz.<br /><br /> Não se esqueça de utilizar os nomes dos contentores válidos quando especificar diretórios virtuais de destino ou blobs. Tenha em atenção que os nomes dos contentores têm de estar em minúsculas. Para regras de nomenclatura de contentor, consulte [nomenclatura e referência de contentores, Blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Disposição:**< mudar o nome de &#124; substituir o não &#124; substituir >|`Optional.`Especifica o comportamento quando um blob com o endereço especificado já existe. Os valores válidos para este parâmetro são: `rename`, `no-overwrite` e `overwrite`. Tenha em atenção que estes valores são maiúsculas e minúsculas. Se for especificado nenhum valor, a predefinição é `rename`.<br /><br /> O valor especificado para este parâmetro afeta todos os ficheiros no diretório especificado pelo `/srcdir` parâmetro.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`Especifica o tipo de blob para os blobs de destino. Os valores válidos são: `BlockBlob` e `PageBlob`. Tenha em atenção que estes valores são maiúsculas e minúsculas. Se for especificado nenhum valor, a predefinição é `BlockBlob`.<br /><br /> Na maioria dos casos, `BlockBlob` é recomendada. Se especificar `PageBlob`, o comprimento de cada ficheiro no diretório tem de ser um múltiplo de 512, o tamanho de uma página para blobs de páginas.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`Caminho para o ficheiro de propriedade para os blobs de destino. Consulte [metadados e o formato de ficheiro de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`Caminho para o ficheiro de metadados para os blobs de destino. Consulte [metadados e o formato de ficheiro de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações.|

### <a name="parameters-for-copying-a-single-file"></a>Parâmetros para copiar um ficheiro único
 Quando copiar um ficheiro único, aplicam os seguintes parâmetros obrigatórios e opcionais:

|Parâmetro de linha de comandos|Descrição|
|----------------------------|-----------------|
|**/srcfile:**< SourceFile\>|`Required.`O caminho completo para o ficheiro a ser copiado. O caminho do diretório tem de ser um caminho absoluto (não um caminho relativo).|
|**/dstblob:**< DestinationBlobPath\>|`Required.`O caminho para o blob de destino na sua conta de armazenamento do Windows Azure. O blob poderá ou poderá já existe.<br /><br /> Especificar o início do nome de blob com o nome do contentor. O nome do blob não pode começar com "/" ou o nome da conta de armazenamento. Para regras de nomenclatura de BLOBs, consulte [nomenclatura e referência de contentores, Blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Quando o contentor de destino é o contentor de raiz, explicitamente tem de especificar `$root` como o contentor, tais como `$root/sample.txt`. Tenha em atenção que os blobs no contentor de raiz não pode incluir "/" nos respetivos nomes.|
|**/ Disposição:**< mudar o nome de &#124; substituir o não &#124; substituir >|`Optional.`Especifica o comportamento quando um blob com o endereço especificado já existe. Os valores válidos para este parâmetro são: `rename`, `no-overwrite` e `overwrite`. Tenha em atenção que estes valores são maiúsculas e minúsculas. Se for especificado nenhum valor, a predefinição é `rename`.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`Especifica o tipo de blob para os blobs de destino. Os valores válidos são: `BlockBlob` e `PageBlob`. Tenha em atenção que estes valores são maiúsculas e minúsculas. Se for especificado nenhum valor, a predefinição é `BlockBlob`.<br /><br /> Na maioria dos casos, `BlockBlob` é recomendada. Se especificar `PageBlob`, o comprimento de cada ficheiro no diretório tem de ser um múltiplo de 512, o tamanho de uma página para blobs de páginas.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`Caminho para o ficheiro de propriedade para os blobs de destino. Consulte [metadados e o formato de ficheiro de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`Caminho para o ficheiro de metadados para os blobs de destino. Consulte [metadados e o formato de ficheiro de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações.|

### <a name="resuming-an-interrupted-copy-session"></a>A retomar a uma sessão de cópia interrompida
 Se uma sessão de cópia é interrompida por qualquer motivo, pode retomá-lo ao executar a ferramenta com o ficheiro do diário de alterações especificado:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Apenas a cópia sessão mais recente se terminou anormalmente, pode ser retomada.

> [!IMPORTANT]
>  Quando retomar uma sessão de cópia, não modifique os ficheiros de origem de dados e diretórios adicionando ou removendo ficheiros.

### <a name="aborting-an-interrupted-copy-session"></a>Abortar uma sessão de cópia interrompida
 Se uma sessão de cópia é interrompida e não é possível retomar (por exemplo, se um diretório de origem foi tentativa inacessível), tem de abortar a sessão atual para que pode ser revertida e podem ser iniciadas novas sessões de cópia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Apenas a última sessão de cópia, se terminou anormalmente, pode ser abortada. Tenha em atenção que não é possível abortar a primeira sessão de cópia para uma unidade. Em vez disso, tem de reiniciar a sessão de cópia com um novo ficheiro de diário de alterações.

## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)
* [Definir propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Referência rápida para comandos utilizados com frequência](storage-import-export-tool-quick-reference-v1.md) 
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparação de uma tarefa de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
