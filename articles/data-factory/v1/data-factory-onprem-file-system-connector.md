---
title: Copiar dados de um sistema de ficheiros utilizando o Azure Data Factory / | Microsoft Docs
description: Saiba como copiar dados de e para um sistema de ficheiros no local utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c58e62a087c456c2e5b9772935aca17b77a75c5e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Copiar dados de e para um sistema de ficheiros no local utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-onprem-file-system-connector.md)
> * [Versão 2 - Pré-visualização](../connector-file-system.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector sistema de ficheiros na V2](../connector-file-system.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para copiar dados de/para um sistema de ficheiros no local. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **de um sistema de ficheiros no local** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **para um sistema de ficheiros no local**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Atividade de cópia não eliminar o ficheiro de origem após ser copiada com êxito para o destino. Se precisar de eliminar o ficheiro de origem depois de uma cópia com êxito, crie uma atividade personalizada para eliminar o ficheiro e utilize a atividade no pipeline. 

## <a name="enabling-connectivity"></a>Ativar a conectividade
Fábrica de dados suporta a ligação e de um sistema de ficheiros no local através de **Data Management Gateway**. Tem de instalar o Data Management Gateway no seu ambiente no local para o serviço fábrica de dados ligar a qualquer arquivo de dados suportados no local, incluindo o sistema de ficheiros. Para saber mais sobre o Data Management Gateway e para obter instruções passo a passo sobre como configurar o gateway, consulte [mover dados entre origens no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). Para além do Data Management Gateway, não existem outros ficheiros binários tem ser instalado para comunicar de e para um sistema de ficheiros no local. Tem de instalar e utilizar o Data Management Gateway, mesmo que esteja o sistema de ficheiros na VM do IaaS do Azure. Para obter informações detalhadas sobre o gateway, consulte [Data Management Gateway](data-factory-data-management-gateway.md).

Para utilizar uma partilha de ficheiros do Linux, instalar [Samba](https://www.samba.org/) no seu servidor Linux e instalar o Data Management Gateway num servidor do Windows. Instalar o Data Management Gateway num servidor Linux não é suportada.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados do sistema de ficheiros utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar um **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines. 
2. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para um sistema de ficheiros no local, pode cria dois serviços ligados para ligar o seu sistema de ficheiros no local e a conta do storage do Azure à fábrica de dados. As propriedades de serviço ligado que são específicas para um sistema de ficheiros no local, consulte [ligado propriedades do serviço](#linked-service-properties) secção.
3. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de blob e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a pasta e o nome de ficheiro (opcional) no seu sistema de ficheiros. As propriedades do conjunto de dados que são específicas do sistema de ficheiros no local, consulte [propriedades do dataset](#dataset-properties) secção.
4. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. No exemplo mencionado anteriormente, utilizar BlobSource como uma origem e FileSystemSink como um sink para a atividade de cópia. Da mesma forma, se estiver a copiar do sistema de ficheiros no local para o Blob Storage do Azure, utilizar FileSystemSource e BlobSink na atividade de cópia. Para as propriedades da atividade de cópia que são específicas do sistema de ficheiros no local, consulte [copiar propriedades da atividade](#copy-activity-properties) secção. Para obter detalhes sobre como utilizar um arquivo de dados como uma origem ou de um receptor de mensagens em fila, clique na ligação na secção anterior para o arquivo de dados.

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para/de um sistema de ficheiros, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-file-system) secção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas ao sistema de ficheiros:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
Pode associar um sistema de ficheiros no local para um Azure data factory com o **local no servidor de ficheiros** serviço ligado. A tabela seguinte fornece descrições para os elementos JSON que são específicas para o serviço de servidor de ficheiros no local ligada.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |Certifique-se de que a propriedade type é definida como **OnPremisesFileServer**. |Sim |
| anfitrião |Especifica o caminho da raiz da pasta que pretende copiar. Utilizar o caráter de escape ' \ ' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos. |Sim |
| ID de utilizador |Especifique o ID do utilizador que tem acesso ao servidor. |Não (se optar por encryptedCredential) |
| palavra-passe |Especifique a palavra-passe para o utilizador (ID de utilizador). |Não (se optar por encryptedCredential |
| encryptedCredential |Especifique as credenciais encriptadas que pode obter executando o cmdlet New-AzureRmDataFactoryEncryptValue. |Não (se optar por especificar ID de utilizador e palavra-passe em texto simples) |
| gatewayName |Especifica o nome do gateway que fábrica de dados deve utilizar para ligar ao servidor de ficheiros no local. |Sim |


### <a name="sample-linked-service-and-dataset-definitions"></a>Serviço ligado e definições do conjunto de dados de exemplo
| Cenário | Na definição de serviço ligado do anfitrião | folderPath na definição do conjunto de dados |
| --- | --- | --- |
| Pasta local no computador do Data Management Gateway: <br/><br/>Exemplos: D:\\ \* ou D:\folder\subfolder\\* |D:\\ \\ (para o Data Management Gateway 2.0 e versões posteriores) <br/><br/> localhost (para versões anteriores a 2.0 do Data Management Gateway) |. \\ \\ ou pasta\\\\subpasta (para o Data Management Gateway 2.0 e versões posteriores) <br/><br/>D:\\ \\ ou d:\\\\pasta\\\\subpasta (para a versão do gateway abaixo 2.0) |
| Pasta partilhada remota: <br/><br/>Exemplos: \\ \\myserver\\partilhar\\ \* ou \\ \\myserver\\partilhar\\pasta\\subpasta\\* |\\\\\\\\MyServer\\\\partilhar |. \\ \\ ou pasta\\\\subpasta |


### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: Utilizar o nome de utilizador e palavra-passe em texto simples

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Exemplo: Utilizar encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções e as propriedades disponíveis para definir os conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A secção de typeProperties é diferente para cada tipo de conjunto de dados. Fornece informações como a localização e o formato dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Especifica o Subcaminho para a pasta. Utilizar o caráter de escape ' \' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter pasta caminhos com base no setor início/fim tempos de data. |Sim |
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando **fileName** não está especificado para um conjunto de dados de saída e **preserveHierarchy** não foram especificadas no receptor de atividade, o nome do ficheiro gerado é o seguinte formato: <br/><br/>`Data.<Guid>.txt`(Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro para ser utilizado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros. <br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (único caráter).<br/><br/>Exemplo 1: "fileFilter": "*. log"<br/>Exemplo 2: "fileFilter": 2014 - 1-?. txt"<br/><br/>Tenha em atenção que fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. |Não |
| partitionedBy |Pode utilizar partitionedBy para especificar um folderPath/fileName dinâmico para dados de séries de tempo. Um exemplo é folderPath parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Não é possível utilizar fileName e fileFilter em simultâneo.

### <a name="using-partitionedby-property"></a>Utilizar a propriedade partitionedBy
Tal como mencionado na secção anterior, pode especificar um folderPath dinâmica e o nome de ficheiro de dados de séries de tempo com o **partitionedBy** propriedade, [funções de Data Factory e as variáveis de sistema](data-factory-functions-variables.md).

Para obter mais detalhes sobre os conjuntos de dados de séries de tempo, agendar e setores de compreender, consulte [criar conjuntos de dados](data-factory-create-datasets.md), [agendamento e execução](data-factory-scheduling-and-execution.md), e [Criar pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exemplo 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {setor} é substituída pelo valor da variável de sistema SliceStart de fábrica de dados no formato (YYYYMMDDHH). SliceStart refere-se a hora do setor. FolderPath é diferente para cada setor. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

```JSON
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

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos para variáveis separadas que utilizam as propriedades folderPath e nome de ficheiro.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, conjuntos de dados e as políticas estão disponíveis para todos os tipos de atividades. Enquanto, propriedades disponíveis no **typeProperties** secção da atividade variar de acordo com cada tipo de atividade.

Para a atividade de cópia, podem variam consoante os tipos de origens e sinks. Se estiver a mover dados de um sistema de ficheiros no local, defina o tipo de origem na atividade de cópia para **FileSystemSource**. Da mesma forma, se estiver a mover dados para um sistema de ficheiros no local, definir o tipo de sink na atividade de cópia para **FileSystemSink**. Esta secção fornece uma lista de propriedades suportadas por FileSystemSource e FileSystemSink.

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é leitura recursivamente as subpastas ou apenas a pasta especificada. |TRUE, False (predefinição) |Não |

**FileSystemSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou sistema de ficheiros. |**PreserveHierarchy:** preserva a hierarquia de ficheiros na pasta de destino. Ou seja, o caminho relativo do ficheiro de origem para a pasta de origem é o mesmo que o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** todos os ficheiros da pasta de origem são criados no primeiro nível da pasta de destino. Os ficheiros de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles:** une todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro name/blob for especificado, o nome de ficheiro intercalado é o nome especificado. Caso contrário, é um nome de ficheiro gerada automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores para as propriedades recursiva e copyBehavior.

| valor de recursiva | valor de copyBehavior | Comportamento resultante |
| --- | --- | --- |
| VERDADEIRO |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| VERDADEIRO |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o destino Pasta1 é criado com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| VERDADEIRO |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o destino Pasta1 é criado com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + ficheiro 5 é intercalado conteúdo para um ficheiro com um nome de ficheiro gerada automaticamente. |
| FALSO |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a estrutura seguinte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Não é escolhido Subfolder1 com File3, File4 e File5. |
| FALSO |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a estrutura seguinte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Não é escolhido Subfolder1 com File3, File4 e File5. |
| FALSO |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a estrutura seguinte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdos são intercalados para um ficheiro com um nome de ficheiro gerada automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/><br/>Não é escolhido Subfolder1 com File3, File4 e File5. |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e compressão suportados
Consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo em detalhes.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Exemplos JSON para copiar dados de e para sistema de ficheiros
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados de e para um sistema de ficheiros no local e o armazenamento de Blobs do Azure. No entanto, pode copiar dados *diretamente* de qualquer uma das origens para qualquer um dos sinks listados no [suportados origens e sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exemplo: Copiar dados de um sistema de ficheiros no local para o armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados de um sistema de ficheiros no local para o Blob storage do Azure. O exemplo tem as seguintes entidades do Data Factory:

* Um serviço ligado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Uma entrada [dataset](data-factory-create-datasets.md) do tipo [FileShare](#dataset-properties).
* Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo seguinte copia dados de séries de tempo de um sistema de ficheiros no local para o armazenamento de Blobs do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções após os exemplos.

Como primeiro passo, configurar o Data Management Gateway de acordo com as instruções em [mover dados entre origens no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço ligado do servidor de ficheiros no local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Recomendamos que utilize o **encryptedCredential** propriedade em vez disso, o **userid** e **palavra-passe** propriedades. Consulte [serviço ligado do servidor de ficheiros](#linked-service-properties) para obter detalhes sobre este serviço ligado.

**Serviço ligado do Storage do Azure:**

```JSON
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

**Conjunto de dados de entrada de sistema de ficheiros no local:**

Dados são captados um novo ficheiro de cada hora. As propriedades de nome de ficheiro e folderPath são determinadas com base na hora de início do setor.  

Definição `"external": "true"` informa fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Conjunto de dados de saída do Blob storage do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e partes de hora a hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
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

**Uma atividade de cópia num pipeline com a origem de sistema de ficheiros e o sink de Blob:**

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource**, e **sink** tipo está definido como **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[  
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exemplo: Copiar dados de SQL Database do Azure para um sistema de ficheiros no local
O exemplo seguinte mostra:

* Um serviço ligado do tipo [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Um serviço ligado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um conjunto de dados de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Um conjunto de dados de saída do tipo [FileShare](#dataset-properties).
* Um pipeline com uma atividade de cópia que utiliza [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) e [FileSystemSink](#copy-activity-properties).

O exemplo copia dados de séries de tempo de uma tabela SQL do Azure para um sistema de ficheiros no local a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções após os exemplos.

**Serviço ligado do SQL Database do Azure:**

```JSON
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

**Serviço ligado do servidor de ficheiros no local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Recomendamos que utilize o **encryptedCredential** propriedade em vez de utilizar o **userid** e **palavra-passe** propriedades. Consulte [serviço ligado do sistema de ficheiros](#linked-service-properties) para obter detalhes sobre este serviço ligado.

**Conjunto de dados de entrada SQL do Azure:**

O exemplo assume que já criou uma tabela "MyTable" no SQL do Azure e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

Definição ``“external”: ”true”`` informa fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
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

**Conjunto de dados de saída de sistema de ficheiros no local:**

Dados são copiados para um novo ficheiro de cada hora. O nome de ficheiro para o blob e folderPath são determinados com base na hora de início do setor.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Uma atividade de cópia num pipeline com a origem SQL e o sink de sistema de ficheiros:**

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource**e o **sink** tipo está definido como **FileSystemSink**. A consulta de SQL Server que está especificada para o **SqlReaderQuery** propriedade seleciona os dados na última hora para copiar.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```


Também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente na definição da atividade de cópia. Para obter mais informações, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
 Para saber mais sobre os principais fatores que afetam o desempenho do movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizá-lo, consulte o [guia Otimização e de desempenho de atividade de cópia](data-factory-copy-activity-performance.md).
