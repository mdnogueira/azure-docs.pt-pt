---
title: Copiar dados de Blob Storage do Azure / | Microsoft Docs
description: 'Saiba como copiar dados de Blobs do Azure Data Factory. Utilizar o nosso exemplo: como copiar dados e de armazenamento de Blobs do Azure e SQL Database do Azure.'
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3f4e0c68542cc38e1c0d90c6589e97134c3845f8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Copiar dados de ou para armazenamento de Blobs do Azure com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-azure-blob-connector.md)
> * [Versão 2 - Pré-visualização](../connector-azure-blob-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector do Blob Storage do Azure no V2](../connector-azure-blob-storage.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para copiar dados para e do armazenamento de Blobs do Azure. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

## <a name="overview"></a>Descrição geral
Pode copiar dados a partir de qualquer arquivo de dados de origem suportada para o Blob Storage do Azure ou a partir do armazenamento de Blobs do Azure para qualquer arquivo de dados suportados sink. A tabela seguinte fornece uma lista de arquivos de dados suportada como origens ou sinks pela atividade de cópia. Por exemplo, pode mover dados **de** uma base de dados do SQL Server ou uma base de dados SQL do Azure **para** um armazenamento de Blobs do Azure. E, pode copiar dados **de** blob storage do Azure **para** um Azure SQL Data Warehouse ou uma coleção de BD do Cosmos do Azure. 

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do Blob Storage do Azure** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **ao Blob Storage do Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]
 
> [!IMPORTANT]
> Atividade de cópia suporta a cópia de dados de/para contas de armazenamento do Azure para fins gerais e de armazenamento de BLOBs de frequente/útil. A atividade suporta o **ler a partir de blocos, acrescentar ou blobs de páginas**, mas suporta **escrita de blobs de blocos apenas**. Armazenamento Premium do Azure não é suportado como um sink porque esteja associada a blobs de páginas.
> 
> Atividade de cópia não elimina dados de origem depois dos dados são copiados com êxito para o destino. Se precisar eliminar dados de origem depois de uma cópia com êxito, crie um [atividade personalizada](data-factory-use-custom-activities.md) para eliminar os dados e utilize a atividade no pipeline. Por exemplo, consulte o [eliminar BLOBs ou pasta de exemplo no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity). 

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de/para um armazenamento de Blobs do Azure utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Este artigo tem um [instruções](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) para criar um pipeline para copiar dados a partir de uma localização de armazenamento de Blobs do Azure para outra localização de armazenamento de Blobs do Azure. Para um tutorial sobre como criar um pipeline para copiar dados de um Blob Storage do Azure para a SQL Database do Azure, consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar um **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines. 
2. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para uma base de dados SQL do Azure, criar dois serviços ligados para ligar a sua conta de armazenamento do Azure e a SQL database do Azure à fábrica de dados. As propriedades de serviço ligado que são específicas para o Blob Storage do Azure, consulte [ligado propriedades do serviço](#linked-service-properties) secção. 
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de blob e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a tabela SQL na base de dados SQL do Azure que contém os dados copiados a partir do blob storage. As propriedades do conjunto de dados que são específicas para o Blob Storage do Azure, consulte [propriedades do dataset](#dataset-properties) secção.
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. No exemplo mencionado anteriormente, utilizar BlobSource como uma origem e SqlSink como um sink para a atividade de cópia. Da mesma forma, se estiver a copiar da SQL Database do Azure para armazenamento de Blobs do Azure, utilizar SqlSource e BlobSink na atividade de cópia. Para as propriedades da atividade de cópia que são específicas para o Blob Storage do Azure, consulte [copiar propriedades da atividade](#copy-activity-properties) secção. Para obter detalhes sobre como utilizar um arquivo de dados como uma origem ou de um receptor de mensagens em fila, clique na ligação na secção anterior para o arquivo de dados.  

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para/de um Blob Storage do Azure, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) secção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas ao Blob Storage do Azure.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
Existem dois tipos de serviços ligados, que pode utilizar para ligar um Storage do Azure a um Azure data factory. São: **AzureStorage** serviço ligado e **AzureStorageSas** serviço ligado. O serviço ligado do Storage do Azure fornece a fábrica de dados com acesso global para o armazenamento do Azure. Enquanto, ligados a SAS de armazenamento do Azure (assinatura de acesso partilhado) o serviço fornece a fábrica de dados com acesso restrito/vínculo de tempo para o armazenamento do Azure. Não existem não existem outras diferenças entre esses dois serviços ligados. Escolha o serviço ligado que se adapta às suas necessidades. As secções seguintes fornecem mais detalhes sobre estes dois serviços ligados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados para representar os dados de entrada ou saídos num armazenamento de Blobs do Azure, definir a propriedade de tipo do conjunto de dados para: **AzureBlob**. Definir o **linkedServiceName** serviço ligado de propriedade do conjunto de dados com o nome do Storage do Azure ou SAS de armazenamento do Azure.  Especifique as propriedades do tipo do conjunto de dados a **contentor do blob** e **pasta** no armazenamento de Blobs.

Para obter uma lista completa de secções JSON & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

Fábrica de dados suporta os seguintes valores do tipo compatível com CLS .NET com base para fornecer informações sobre o tipo de "estrutura" para origens de dados de esquema no leitura como BLOBs do Azure: Int16, Int32, Int64, único, Double, Decimal, Byte [] e String, booleano, Datetime, e Guid Datetimeoffset, Timespan. Fábrica de dados efetua automaticamente conversões de tipo quando mover dados de um arquivo de dados de origem para um arquivo de dados do sink.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização, etc., formato dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **AzureBlob** dataset tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para o contentor e a pasta no armazenamento de Blobs. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |Nome do blob. nome de ficheiro é opcional e maiúsculas e minúsculas.<br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no Blob específico.<br/><br/>Quando o nome de ficheiro não for especificado, a cópia inclui todos os Blobs no folderPath para conjunto de dados de entrada.<br/><br/>Quando **fileName** não está especificado para um conjunto de dados de saída e **preserveHierarchy** não foram especificadas no receptor de atividade, o nome do ficheiro gerado seria a seguir este formato: Data.<Guid>.txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy propriedade é opcional. Pode utilizá-lo para especificar um folderPath dinâmica e o nome de ficheiro de dados de séries de tempo. Por exemplo, folderPath pode ser parametrizada para cada hora dos dados. Consulte o [utilizando a secção de propriedade partitionedBy](#using-partitionedBy-property) para obter detalhes e exemplos. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="using-partitionedby-property"></a>Utilizar a propriedade partitionedBy
Tal como mencionado na secção anterior, pode especificar um folderPath dinâmica e o nome de ficheiro de dados de séries de tempo com o **partitionedBy** propriedade, [funções de Data Factory e as variáveis de sistema](data-factory-functions-variables.md).

Para obter mais informações sobre conjuntos de dados de séries de tempo, agendar e setores, consulte [criar conjuntos de dados](data-factory-create-datasets.md) e [agendamento e execução](data-factory-scheduling-and-execution.md) artigos.

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {setor} é substituída pelo valor da variável de sistema do Data Factory SliceStart no formato (YYYYMMDDHH) especificado. SliceStart refere-se a hora do setor. FolderPath é diferente para cada setor. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Exemplo 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em separado variáveis que são utilizadas pelas propriedades folderPath e nome de ficheiro.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, conjuntos de dados e as políticas estão disponíveis para todos os tipos de atividades. Enquanto, propriedades disponíveis no **typeProperties** secção da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks. Se estiver a mover dados de um Blob Storage do Azure, defina o tipo de origem na atividade de cópia para **BlobSource**. Da mesma forma, se estiver a mover dados para um armazenamento de Blobs do Azure, definir o tipo de sink na atividade de cópia para **BlobSink**. Esta secção fornece uma lista de propriedades suportadas por BlobSource e BlobSink.

**BlobSource** suporta as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada. |TRUE (valor predefinido), False |Não |

**BlobSink** suporta as seguintes propriedades **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou sistema de ficheiros. |<b>PreserveHierarchy</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível da pasta de destino. Os ficheiros de destino tem o nome de automaticamente gerado. <br/><br/><b>MergeFiles</b>: une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro/Blob, o nome de ficheiro intercalado seria o nome especificado; caso contrário, seria nome de ficheiro gerada automaticamente. |Não |

**BlobSource** também suporta estas duas propriedades para compatibilidade com versões anteriores.

* **treatEmptyAsNull**: Especifica se tratar de uma cadeia nula ou vazia como valor nulo.
* **skipHeaderLineCount** -Especifica o número de linhas tem de ser ignoradas. É aplicável apenas ao conjunto de dados de entrada está a utilizar TextFormat.

Da mesma forma, **BlobSink** suporta a seguinte propriedade para compatibilidade com versões anteriores.

* **blobWriterAddHeader**: Especifica se pretende adicionar um cabeçalho de definições da coluna ao escrever um conjunto de dados de saída.

Conjuntos de dados agora suportam as seguintes propriedades que implementem a mesma funcionalidade: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

A tabela seguinte fornece orientações sobre como utilizar as novas propriedades do conjunto de dados em vez destas propriedades de origem/sink do blob.

| Propriedade da atividade de cópia | Propriedade de conjunto de dados |
|:--- |:--- |
| skipHeaderLineCount no BlobSource |skipLineCount e firstRowAsHeader. As linhas são ignoradas pela primeira vez e, em seguida, a primeira linha é lido como um cabeçalho. |
| treatEmptyAsNull no BlobSource |treatEmptyAsNull no conjunto de dados de entrada |
| blobWriterAddHeader no BlobSink |firstRowAsHeader no conjunto de dados de saída |

Consulte [especificando TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) secção para obter informações detalhadas sobre estas propriedades.    

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| Recursiva | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| VERDADEIRO |preserveHierarchy |Para uma pasta de origem Pasta1 com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura de origem<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| VERDADEIRO |flattenHierarchy |Para uma pasta de origem Pasta1 com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o destino Pasta1 é criado com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| VERDADEIRO |mergeFiles |Para uma pasta de origem Pasta1 com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o destino Pasta1 é criado com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + ficheiro 5 é intercalado conteúdo para um ficheiro com nome de ficheiro gerado automaticamente |
| FALSO |preserveHierarchy |Para uma pasta de origem Pasta1 com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |
| FALSO |flattenHierarchy |Para uma pasta de origem Pasta1 com a estrutura seguinte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |
| FALSO |mergeFiles |Para uma pasta de origem Pasta1 com a estrutura seguinte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdos são intercalados para um ficheiro com nome de ficheiro gerada automaticamente. nome gerado automaticamente para File1<br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Instruções: Assistente de cópia de utilização para copiar dados para/de armazenamento de BLOBs
Vamos ver como rapidamente copiar dados de/para um armazenamento de Blobs do Azure. Nestas instruções, armazena os dados de origem e destino do tipo: Blob Storage do Azure. O pipeline nestas instruções copia dados a partir de uma pasta para outra pasta no mesmo contentor de blob. Estas instruções é intencionalmente simples para lhe mostrar propriedades ou de definições ao utilizar o armazenamento de Blob como uma origem ou o sink. 

### <a name="prerequisites"></a>Pré-requisitos
1. Criar um para fins gerais **conta do Storage do Azure** se ainda não tiver um. Utilizar o armazenamento de BLOBs como ambos **origem** e **destino** armazenam dados nestas instruções. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter os passos para criar uma.
2. Criar um contentor do blob denominado **adfblobconnector** na conta de armazenamento. 
4. Crie uma pasta denominada **entrada** no **adfblobconnector** contentor.
5. Crie um ficheiro denominado **emp.txt** com o seguinte conteúdo e carregá-la para o **entrada** pasta utilizando ferramentas como [Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>Criar fábrica de dados
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **+ novo** no canto superior esquerdo, clique em **Intelligence + análise**e clique em **Data Factory**.
3. No painel **Nova fábrica de dados**:   
    1. Introduza **ADFBlobConnectorDF** para o **nome**. O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: `*Data factory name “ADFBlobConnectorDF” is not available`, alterar o nome da fábrica de dados (por exemplo, yournameADFBlobConnectorDF) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
    2. Selecione a sua **subscrição** do Azure.
    3. Para o grupo de recursos, selecione **utilização existente** para selecionar um grupo de recursos existente (ou) selecione **criar nova** para introduzir um nome para um grupo de recursos.
    4. Selecione uma **localização** para a fábrica de dados.
    5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.
    6. Clique em **Criar**.
3. Depois de concluída a criação, consulte o **Data Factory** painel conforme mostrado na imagem seguinte: ![home page da fábrica de dados](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Assistente de Cópia
1. Na home page do Data Factory, clique em de **copiar dados [pré-visualização]** mosaico para iniciar o **Assistente de cópia de dados** num separador separado.    
    
    > [!NOTE]
    >    Se vir que o browser é bloqueado no "A autorizar …", desative/desmarque **bloquear cookies de terceiros e dados do site** definição (ou) mantenha-a ativada e crie uma exceção para **login.microsoftonline.com**e, em seguida, tente iniciar novamente o assistente.
2. Na página **Propriedades**:
    1. Introduza **CopyPipeline** para **nome da tarefa**. O nome da tarefa é o nome do pipeline na fábrica de dados.
    2. Introduza um **Descrição** da tarefa (opcional).
    3. Para **cadência de tarefas ou agenda de tarefas**, mantenha o **regularmente executado numa agenda** opção. Se pretender executar esta tarefa apenas uma vez em vez de ser executado repetidamente numa agenda, selecione **executar agora uma vez**. Se selecionar, **executar agora uma vez** opção, uma [pipeline Monouso](data-factory-create-pipelines.md#onetime-pipeline) é criado. 
    4. Manter as definições para **periódica padrão**. Esta tarefa é executada diariamente entre os tempos de início e de fim que especificar no próximo passo.
    5. Alterar o **data hora de início** para **21/04/2017**. 
    6. Alterar o **data hora de fim** para **25/04/2017**. Pode querer escreva a data em vez de navegar pelas calendário.     
    8. Clique em **Seguinte**.
      ![Ferramenta copiar – página de propriedades](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png) 
3. Na página **Arquivo de dados de origem**, clique no mosaico **Blob Storage do Azure**. Utilize esta página para especificar o arquivo de dados de origem para a tarefa de cópia. Pode utilizar um serviço ligado do arquivo de dados existente (ou) especificar um novo arquivo de dados. Para utilizar um serviço ligado existente, deverá selecionar **de serviços ligados existentes** e selecione o serviço ligado correto. 
    ![Ferramenta copiar – página do arquivo de dados de origem](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de blobs do Azure**:
   1. Mantenha o nome gerado automaticamente para **nome da ligação**. O nome da ligação é o nome do serviço ligado do tipo: armazenamento do Azure. 
   2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
   3. Selecione a sua subscrição do Azure ou manter **Selecionar tudo** para **subscrição do Azure**.   
   4. Selecione uma **Conta de armazenamento do Azure** na lista das contas de armazenamento do Azure disponíveis na subscrição selecionada. Também pode optar por introduzir as definições de conta de armazenamento manualmente selecionando **introduzir manualmente** opção para o **método de seleção de contas**.
   5. Clique em **Seguinte**. 
      ![Ferramenta copiar – especificar a conta de armazenamento de Blobs do Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na página **Escolher o ficheiro ou pasta de entrada**:
   1. Faça duplo clique em **adfblobcontainer**.
   2. Selecione **entrada**e clique em **escolha**. Nestas instruções, selecione a pasta de entrada. Pode também selecionar o ficheiro emp.txt na pasta em vez disso. 
      ![Ferramenta copiar – escolha a pasta ou ficheiro de entrada](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. No **escolher o ficheiro de entrada ou a pasta** página:
    1. Confirme que o **ficheiro ou pasta** está definido como **adfblobconnector/entrada**. Se os ficheiros estão em subpastas, por exemplo, 2017/04/01, 2017/04/02 e assim sucessivamente, introduza adfblobconnector/valor / {year} / {month} / {day} para o ficheiro ou pasta. Quando prime SEPARADOR fora da caixa de texto, consulte três na lista pendente para selecionar os formatos de (AAAA) de ano, mês (MM) e dia (dd). 
    2. Não defina **copiar recursivamente ficheiro**. Selecione esta opção para recursivamente passam através de pastas para os ficheiros a ser copiado para o destino. 
    3. Não o **cópia binária** opção. Selecione esta opção para efetuar uma cópia do ficheiro de origem para o destino de binária. Não selecione para estas instruções para que possa ver mais opções nas páginas seguintes. 
    4. Confirme que o **tipo de compressão** está definido como **nenhum**. Selecione um valor para esta opção se os ficheiros de origem são comprimidos dos formatos suportados. 
    5. Clique em **Seguinte**.
    ![Ferramenta copiar – escolha a pasta ou ficheiro de entrada](./media/data-factory-azure-blob-connector/chose-input-file-folder.png) 
7. Na página **Definições do formato de ficheiro**, pode ver os delimitadores e o esquema que é detetado automaticamente pelo assistente ao analisar o ficheiro. 
    1. Confirme as seguintes opções: uma. O **formato de ficheiro** está definido como **formato de texto**. Pode ver todos os formatos suportados na lista pendente. Por exemplo: JSON, Avro, ORC, Parquet.
        b. O **delimitador de coluna** está definido como `Comma (,)`. Pode ver os delimitadores de coluna suportados pela fábrica de dados na lista pendente. Também pode especificar um delimitador personalizado.
        c. O **delimitador de linha** está definido como `Carriage Return + Line feed (\r\n)`. Pode ver os delimitadores de linha suportados pela fábrica de dados na lista pendente. Também pode especificar um delimitador personalizado.
        d. O **ignorar a contagem de linha** está definido como **0**. Se pretender que as linhas de alguns a ser ignorada na parte superior do ficheiro, introduza o número aqui.
        e.  O **primeira linha de dados contém nomes de colunas** não está definido. Se os ficheiros de origem contém nomes de colunas na primeira linha, selecione esta opção.
        f. O **tratar o valor de coluna vazia como null** opção estiver definida.
    2. Expanda **definições avançadas** para ver a opção avançada disponível.
    3. Na parte inferior da página, consulte o **pré-visualização** dos dados a partir do ficheiro emp.txt.
    4. Clique em **esquema** separador na parte inferior para ver o esquema que o Assistente para copiar inferido ao observar os dados no ficheiro de origem.
    5. Clique em **Seguinte** depois de rever os delimitadores e pré-visualizar os dados.
    ![Ferramenta copiar – definições do formato de ficheiro](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)  
8. No **página do arquivo de dados de destino**, selecione **Blob Storage do Azure**e clique em **seguinte**. Estão a utilizar o armazenamento de Blobs do Azure como ambos os arquivos de dados de origem e destino nestas instruções.    
    ![Ferramenta copiar – arquivo de dados de destino selecione](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. No **especifique a conta de armazenamento de Blobs do Azure** página:
   1. Introduza **AzureStorageLinkedService** para o **nome da ligação** campo.
   2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
   3. Selecione a sua **subscrição** do Azure.  
   4. Selecione a sua conta de armazenamento do Azure. 
   5. Clique em **Seguinte**.     
10. No **escolha a pasta ou ficheiro de saída** página: 
    6. Especifique **caminho da pasta** como **adfblobconnector/saída / {year} / {month} / {day}**. Introduza **SEPARADOR**.
    7. Para o **ano**, selecione **aaaa**.
    8. Para o **mês**, confirme que está definido como **MM**.
    9. Para o **dia**, confirme que está definido como **dd**.
    10. Confirme que o **tipo de compressão** está definido como **nenhum**.
    11. Confirme que o **copiar comportamento** está definido como **intercalar ficheiros**. Se já existir no ficheiro de saída com o mesmo nome, o novo conteúdo é adicionado ao mesmo ficheiro no final.
    12. Clique em **Seguinte**.
    ![Ferramenta copiar – escolha a pasta ou ficheiro de saída](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. No **definições do formato de ficheiro** página, reveja as definições e clique em **seguinte**. Uma das opções adicionais aqui está a adicionar um cabeçalho de ficheiro de saída. Se selecionar esta opção, uma linha de cabeçalho é adicionada com os nomes das colunas do esquema da origem. Pode mudar o nome dos nomes de coluna predefinido ao visualizar o esquema para a origem. Por exemplo, pode alterar a primeira coluna para o nome próprio e apelido segunda coluna. Em seguida, o ficheiro de saída é gerado com um cabeçalho com estes nomes, como nomes de colunas. 
    ![Ferramenta copiar – definições do formato de ficheiro de destino](media/data-factory-azure-blob-connector/file-format-destination.png)
12. No **definições de desempenho** página, confirme que **nuvem unidades** e **paralela cópias** estão definidas como **automática**e clique em seguinte. Para obter detalhes sobre estas definições, consulte [copiar guia Otimização e de desempenho de atividade](data-factory-copy-activity-performance.md#parallel-copy).
    ![Ferramenta copiar – definições de desempenho](media/data-factory-azure-blob-connector/copy-performance-settings.png) 
14. No **resumo** página, reveja as definições de todos os (propriedades da tarefa, as definições de origem e de destino e as definições de cópia) e clique em **seguinte**.
    ![Ferramenta copiar – página de resumo](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Reveja as informações na página **Resumo** e clique em **Concluir**. O assistente cria dois serviços ligados, dois conjuntos de dados (entrada e saída) e um pipeline na fábrica de dados (a partir da qual iniciou o Assistente de Cópia).
    ![Ferramenta copiar – página de implementação](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorizar o pipeline (tarefa de cópia)

1. Clique na ligação `Click here to monitor copy pipeline` no **implementação** página. 
2. Deverá ver o **monitorizar e gerir aplicações** num separador separado.  ![Monitorizar e gerir aplicações](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Alterar o **iniciar** tempo no topo `04/19/2017` e **final** altura de `04/27/2017`e, em seguida, clique em **aplicar**. 
4. Deverá ver cinco janelas de atividade do **ATIVIDADE WINDOWS** lista. O **WindowStart** vezes devem abranger todos os dias do início de pipeline ao pipeline de hora de fim. 
5. Clique em **atualizar** botão para o **ATIVIDADE WINDOWS** lista algumas horas até ver o estado de todas as janelas de atividade está definida como pronta. 
6. Agora, certifique-se de que os ficheiros de saída são gerados na pasta de saída do contentor de adfblobconnector. Deverá ver a seguinte estrutura de pasta na pasta de saída: 
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25    
    ```
Para obter informações detalhadas sobre como monitorizar e gerir as fábricas de dados, consulte [monitorizar e gerir o pipeline do Data Factory](data-factory-monitor-manage-app.md) artigo. 
 
### <a name="data-factory-entities"></a>Entidades da fábrica de dados
Agora, mude novamente para o separador com a home page da fábrica de dados. Tenha em atenção que existem dois serviços ligados, dois conjuntos de dados e um pipeline na fábrica de dados agora. 

![Página inicial de fábrica de dados com entidades](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Clique em **autor e implementar** para iniciar o Editor do Data Factory. 

![Editor do Data Factory](media/data-factory-azure-blob-connector/data-factory-editor.png)

Deverá ver as seguintes entidades do Data Factory na fábrica de dados: 

 - Dois serviços ligados. Uma para a origem e dos outros de destino. Consulte os serviços ligados para a mesma conta de armazenamento do Azure esta explicação passo a passo. 
 - Dois conjuntos de dados. Um conjunto de dados de entrada e um conjunto de dados de saída. Nestas instruções, ambos utilizam o mesmo contentor de blob, mas Consulte pastas diferentes (entrada e saída).
 - Um pipeline. O pipeline contém uma atividade de cópia que utiliza uma origem de blob e um receptor de BLOBs para copiar dados a partir de uma localização de blob do Azure para outra localização de blob do Azure. 

As secções seguintes fornecem mais informações sobre estas entidades. 

#### <a name="linked-services"></a>Serviços ligados
Deverá ver dois serviços ligados. Uma para a origem e dos outros de destino. Nestas instruções, ambas as definições de têm o mesmo aspeto, exceto para os nomes. O **tipo** do serviço ligado está definido como **AzureStorage**. A propriedade mais importante da definição de serviço ligado é o **connectionString**, que é utilizada pela fábrica de dados para ligar à sua conta de armazenamento do Azure no tempo de execução. Ignore a propriedade hubName na definição do. 

##### <a name="source-blob-storage-linked-service"></a>Serviço de ligado do armazenamento de BLOBs de origem
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Serviço de ligado do armazenamento de BLOBs de destino

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Para mais informações sobre o serviço ligado do Storage do Azure, consulte [ligado propriedades do serviço](#linked-service-properties) secção. 

#### <a name="datasets"></a>Conjuntos de dados
Existem dois conjuntos de dados: um conjunto de dados de entrada e um conjunto de dados de saída. O tipo do conjunto de dados está definido como **AzureBlob** para ambos. 

O conjunto de dados de entrada aponta para o **entrada** pasta o **adfblobconnector** contentor do blob. O **externo** propriedade está definida como **verdadeiro** deste conjunto de dados que os dados não são produzidos pelo pipeline com a atividade de cópia que demora este conjunto de dados como entrada. 

O conjunto de dados de saída aponta para o **saída** na pasta do mesmo contentor de blob. O conjunto de dados de saída também utiliza o ano, mês e dia o **SliceStart** variável sistema dinamicamente avaliar o caminho para o ficheiro de saída. Para obter uma lista de funções e variáveis do sistema suportadas pela fábrica de dados, consulte [funções de Data Factory e variáveis do sistema](data-factory-functions-variables.md). O **externo** propriedade está definida como **falso** (valor predefinido) porque este conjunto de dados é produzido pelo pipeline. 

Para obter mais informações sobre as propriedades suportadas pelo conjunto de dados de Blobs do Azure, consulte [propriedades do Dataset](#dataset-properties) secção.

##### <a name="input-dataset"></a>Conjunto de dados de entrada

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Conjunto de dados de saída

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pipeline
O pipeline tem apenas uma atividade. O **tipo** da atividade está definida como **cópia**.  As propriedades de tipo para a atividade, existem duas secções, uma para a origem e dos outros para sink. O tipo de origem está definido como **BlobSource** como a atividade está a copiar dados de um armazenamento de Blobs. O tipo de sink está definido como **BlobSink** como a atividade copiar dados para um armazenamento de Blobs. A atividade de cópia demora InputDataset-z4y como entrada e OutputDataset-z4y como resultado. 

Para obter mais informações sobre propriedades suportadas por BlobSource e BlobSink, consulte [copiar propriedades da atividade](#copy-activity-properties) secção. 

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Exemplos JSON para copiar dados para e do armazenamento de BLOBs  
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados e de armazenamento de Blobs do Azure e SQL Database do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Exemplo JSON: Copiar dados de armazenamento de BLOBs para base de dados SQL
O exemplo seguinte mostra:

1. Um serviço ligado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [BlobSource](#copy-activity-properties) e [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

As cópias de exemplo séries de tempo dados a partir de um Azure blob para um SQL do Azure a hora a hora de tabela. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado SQL do Azure:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço ligado do Storage do Azure:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
O Azure Data Factory suportam dois tipos de serviços de armazenamento do Azure ligado: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de ligação que inclua a chave de conta e para um posterior, especificar o Uri de assinatura de acesso partilhado (SAS). Consulte [serviços ligados](#linked-service-properties) secção para obter detalhes.  

**Conjunto de dados de entrada Blob do Azure:**

Dados são captados um blob de novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome de ficheiro utiliza a parte de hora a hora de início. "external": "true" definição informa fábrica de dados que a tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Conjunto de dados de saída do SQL do Azure:**

Os dados de cópias de exemplo para uma tabela com o nome "MyTable" numa base de dados SQL do Azure. Crie a tabela na base de dados SQL do Azure com o mesmo número de colunas como espera que o ficheiro CSV de Blob que contém. Novas linhas são adicionadas à tabela de cada hora.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Uma atividade de cópia num pipeline com a origem de Blob e o sink do SQL Server:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Exemplo JSON: Copiar dados de SQL do Azure para BLOBs do Azure
O exemplo seguinte mostra:

1. Um serviço ligado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [BlobSink](#copy-activity-properties).

O exemplo copia dados de séries de tempo de uma tabela SQL do Azure para um blob do Azure numa base horária. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado SQL do Azure:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço ligado do Storage do Azure:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
O Azure Data Factory suportam dois tipos de serviços de armazenamento do Azure ligado: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de ligação que inclua a chave de conta e para um posterior, especificar o Uri de assinatura de acesso partilhado (SAS). Consulte [serviços ligados](#linked-service-properties) secção para obter detalhes.  

**Conjunto de dados de entrada SQL do Azure:**

O exemplo assume que criou uma tabela "MyTable" SQL do Azure e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

A definição "external": "true" informa serviço fábrica de dados que a tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime",  "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Uma atividade de cópia num pipeline com a origem SQL e o sink de Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **SqlReaderQuery** propriedade seleciona os dados na última hora para copiar.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureSQLInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
