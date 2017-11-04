---
title: Mover dados do HDFS no local | Microsoft Docs
description: Saiba mais sobre como mover dados do HDFS no local utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 85a3b1b96effb716b8a33da8ad37309462042a44
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover dados do HDFS no local utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-hdfs-connector.md)
> * [Versão 2 - Pré-visualização](../connector-hdfs.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector HDFS na V2](../connector-hdfs.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de um HDFS no local. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados do HDFS para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Fábrica de dados atualmente suporta apenas mover dados a partir de um HDFS no local para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um HDFS no local.

> [!NOTE]
> Atividade de cópia não eliminar o ficheiro de origem após ser copiada com êxito para o destino. Se precisar de eliminar o ficheiro de origem depois de uma cópia com êxito, crie uma atividade personalizada para eliminar o ficheiro e utilize a atividade no pipeline. 

## <a name="enabling-connectivity"></a>Ativar a conectividade
Serviço de fábrica de dados suporta a ligar ao HDFS no local utilizando o Data Management Gateway. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway. Utilize o gateway para ligar ao HDFS, mesmo esteja alojado numa VM do IaaS do Azure.

> [!NOTE]
> Certifique-se com o Data Management Gateway pode aceder **todos os** [nó servidor de nomes de]: [nome de porta de nó] e [servidores de nó de dados]: [porta de dados do nó] do cluster de Hadoop. Predefinição [nome do porta nó] é 50070, não sendo predefinido [porta de dados do nó] 50075.

Apesar de poder instalar o gateway no mesmo computador no local ou VM do Azure como o HDFS, recomendamos a instalação do gateway num máquina/Azure separado IaaS VM. Ter o gateway num computador separado reduz a contenção de recursos e melhora o desempenho. Quando instalar o gateway num computador separado, a máquina deve ser capaz de aceder a máquina com o HDFS.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de uma origem HDFS utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado.

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados do HDFS, consulte [exemplo JSON: copiar dados do HDFS no local para o Blob do Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) secção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir o HDFS entidades do Data Factory específicas:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
Um serviço ligado liga um arquivo de dados para uma fábrica de dados. Criar um serviço ligado do tipo **Hdfs** para ligar um HDFS no local à fábrica de dados. A tabela seguinte fornece uma descrição para os elementos JSON específicos HDFS serviço ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **Hdfs** |Sim |
| Url |URL para o HDFS |Sim |
| authenticationType |Anónimo, ou o Windows. <br><br> Para utilizar **a autenticação Kerberos** para o conector do HDFS, consulte [nesta secção](#use-kerberos-authentication-for-hdfs-connector) para configurar o seu ambiente no local em conformidade. |Sim |
| Nome de utilizador |Autenticação de nome de utilizador do Windows. Para a autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para autenticação do Windows. |Sim (para autenticação do Windows) |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar para o HDFS. |Sim |
| encryptedCredential |[Novo AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) saída da credencial de acesso. |Não |

### <a name="using-anonymous-authentication"></a>Autenticação anónima

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Utilizando a autenticação Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **FileShare** (que inclui o conjunto de dados do HDFS) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo:`myfolder`<br/><br/>Utilize o caráter de escape ' \ ' para carateres especiais na cadeia. Por exemplo: para folder\subfolder, especifique a pasta\\\\subpasta e para d:\samplefolder, especifique d:\\\\samplefolder.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter pasta caminhos com base no setor início/fim tempos de data. |Sim |
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro de dados de séries de tempo. Exemplo: folderPath parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizados em simultâneo.

### <a name="using-partionedby-property"></a>Utilizar a propriedade partionedBy
Tal como mencionado na secção anterior, pode especificar um folderPath dinâmica e o nome de ficheiro de dados de séries de tempo com o **partitionedBy** propriedade, [funções de Data Factory e as variáveis de sistema](data-factory-functions-variables.md).

Para obter mais informações sobre conjuntos de dados de séries de tempo, agendar e setores, consulte [criar conjuntos de dados](data-factory-create-datasets.md), [agendamento e execução](data-factory-scheduling-and-execution.md), e [criar Pipelines](data-factory-create-pipelines.md) artigos.

#### <a name="sample-1"></a>Exemplo 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo é substituído {setor} com o valor da variável de sistema do Data Factory SliceStart no formato (YYYYMMDDHH) especificado. SliceStart refere-se a hora do setor. FolderPath é diferente para cada setor. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

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
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em separado variáveis que são utilizadas pelas propriedades folderPath e nome de ficheiro.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e as políticas estão disponíveis para todos os tipos de atividades.

Enquanto, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Para a atividade de cópia, quando a origem é do tipo **FileSystemSource** na secção typeProperties, estão disponíveis as seguintes propriedades:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e compressão suportados
Consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo em detalhes.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemplo JSON: copiar dados do HDFS no local para o Blob do Azure
Este exemplo mostra como copiar dados de um HDFS no local para o Blob Storage do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.  

O exemplo fornece definições de JSON para as seguintes entidades do Data Factory. Pode utilizar estas definições para criar um pipeline para copiar dados de HDFS ao Blob Storage do Azure utilizando [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Um serviço ligado do tipo [OnPremisesHdfs](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [FileShare](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados a partir um HDFS no local para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

Como primeiro passo, configure o data management gateway. As instruções de [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado do HDFS:** este exemplo utiliza a autenticação do Windows. Consulte [serviço ligado do HDFS](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Serviço ligado do Storage do Azure:**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de dados de entrada do HDFS:** este conjunto de dados refere-se para a pasta HDFS DataTransfer/UnitTest /. O pipeline copia todos os ficheiros nesta pasta para o destino.

A definição "external": "true" informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Uma atividade de cópia num pipeline com a origem de sistema de ficheiros e o sink de Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **consulta** propriedade seleciona os dados na última hora para copiar.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilizar a autenticação Kerberos para o conector do HDFS
Existem duas opções para configurar o ambiente no local, de modo a utilizar a autenticação Kerberos no conector do HDFS. Pode escolher a melhor se adequa a maiúsculas e minúsculas.
* Opção 1: [máquina do gateway de associação no Kerberos realm](#kerberos-join-realm)
* Opção 2: [ativar a fidedignidade mútua entre o domínio do Windows e Kerberos realm](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: Associar a máquina do gateway no Kerberos realm

#### <a name="requirement"></a>Requisito:

* A máquina do gateway tem de associar o realm Kerberos e não é possível associar a qualquer domínio Windows.

#### <a name="how-to-configure"></a>Como configurar:

**No computador do gateway:**

1.  Execute o **Ksetup** utilitário para configurar o servidor do KDC do Kerberos e realm.

    A máquina tem de ser configurada como um membro do grupo de trabalho, uma vez que um realm Kerberos é diferente de um domínio Windows. Isto pode ser alcançado ao definir o realm Kerberos e adicionar um servidor do KDC da seguinte forma. Substitua *REALM.COM* com o seus respetivo realm conforme necessário.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reiniciar** máquina depois de executar estes 2 comandos.

2.  Verificar a configuração com **Ksetup** comando. O resultado deve ser, como:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**De Azure Data Factory:**

* Configurar o conector do HDFS utilizando **autenticação do Windows** juntamente com o nome principal Kerberos e a palavra-passe para ligar à origem de dados de HDFS. Verifique [propriedades de serviço ligado do HDFS](#linked-service-properties) secção nos detalhes de configuração.

### <a name="kerberos-mutual-trust"></a>Opção 2: Ativar a fidedignidade mútua entre o domínio do Windows e Kerberos realm

#### <a name="requirement"></a>Requisito:
*   A máquina do gateway tem de associar um domínio Windows.
*   Necessita de permissão para atualizar as definições do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar:

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial seguinte com os seus respetivo realm e o controlador de domínio, conforme necessário.

**No servidor do KDC:**

1.  Edite a configuração do KDC em **krb5.conf** ficheiro para permitir que o KDC confiança de domínio do Windows que faça referência ao modelo de configuração seguintes. Por predefinição, a configuração está localizada em **/etc/krb5.conf**.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  **Reiniciar** o serviço KDC após a configuração.

2.  Preparar um principal com o nome  **krbtgt/REALM.COM@AD.COM**  no servidor do KDC com o seguinte comando:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  No **hadoop.security.auth_to_local** a configuração do serviço HDFS do ficheiro, adicione `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**No controlador de domínio:**

1.  Execute o seguinte **Ksetup** comandos para adicionar uma entrada do realm:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabelece confiança do domínio do Windows para Kerberos Realm. [palavra-passe] é a palavra-passe para o principal  **krbtgt/REALM.COM@AD.COM** .

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione o algoritmo de encriptação utilizado no Kerberos.

    1. Aceda ao Gestor de servidor > Gestão de política de grupo > domínio > objetos de política de grupo > predefinido ou política de domínio do Active Directory e editar.

    2. No **Editor de gestão de políticas de grupo** janela de pop-up, aceda a configuração do computador > políticas > definições do Windows > definições de segurança > Políticas locais > Opções de segurança e configurar **rede segurança: configurar os tipos de encriptação permitidos para Kerberos**.

    3. Selecione o algoritmo de encriptação que pretende utilizar quando ligar ao KDC. Normalmente, pode selecionar simplesmente todas as opções.

        ![Tipos de encriptação de configuração de Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Utilize **Ksetup** comandos para especificar o algoritmo de encriptação a utilizar sobre o REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e um principal de Kerberos, para poder utilizar o Kerberos principal no domínio Windows.

    1. Iniciar as ferramentas administrativas > **computadores e utilizadores do Active Directory**.

    2. Configurar funcionalidades avançadas clicando **vista** > **funcionalidades avançadas**.

    3. Localizar a conta à qual pretende criar mapeamentos e faça duplo clique para ver **mapeamentos de nome** > clique **Kerberos nomes** separador.

    4. Adicione um principal do realm.

        ![Identidade de segurança do mapa](media/data-factory-hdfs-connector/map-security-identity.png)

**No computador do gateway:**

* Execute o seguinte **Ksetup** comandos para adicionar uma entrada do realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**De Azure Data Factory:**

* Configurar o conector do HDFS utilizando **autenticação do Windows** juntamente com a sua conta de domínio ou Principal de Kerberos para ligar à origem de dados de HDFS. Verifique [propriedades de serviço ligado do HDFS](#linked-service-properties) secção nos detalhes de configuração.

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
