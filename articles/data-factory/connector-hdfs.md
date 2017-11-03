---
title: Copiar dados de HDFS utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de uma origem HDFS nuvem ou no local para os arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jingwang
ms.openlocfilehash: 6d28b82f892ac88c32ab3e04df19143d69a5a05b
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="copy-data-from-and-to-hdfs-using-azure-data-factory"></a>Copiar dados de origem e de HDFS utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-hdfs-connector.md)
> * [Versão 2 - Pré-visualização](connector-hdfs.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de origem e de HDFS. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector HDFS no V1](v1/data-factory-hdfs-connector.md).


## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados do HDFS para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector HDFS suporta:

- Copiar ficheiros utilizando **Windows** (Kerberos) ou **anónimo** autenticação.
- Copiar ficheiros utilizando **webhdfs** protocolo ou **DistCp incorporada** suportar.
- Copiar ficheiros como-está ou ficheiros com a análise/gerar o [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de/para um HDFS que não está acessível publicamente, terá de configurar um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](concepts-integration-runtime.md) artigo para obter detalhes.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir o HDFS entidades do Data Factory específicas.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do HDFS:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Hdfs**. | Sim |
| URL |URL para o HDFS |Sim |
| authenticationType | Valores permitidos são: **anónimo**, ou **Windows**. <br><br> Para utilizar **a autenticação Kerberos** para o conector do HDFS, consulte [nesta secção](#use-kerberos-authentication-for-hdfs-connector) para configurar o seu ambiente no local em conformidade. |Sim |
| Nome de utilizador |Autenticação de nome de utilizador do Windows. Para a autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para autenticação do Windows. Marcar este campo como SecureString. |Sim (para autenticação do Windows) |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração Self-hosted ou Runtime de integração do Azure (se o arquivo de dados acessível publicamente). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo: utilizar a autenticação anónima**

```json
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: através da autenticação Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados do HDFS.

Para copiar dados de HDFS, defina a propriedade de tipo do conjunto de dados para **FileShare**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **partilha de ficheiros** |Sim |
| folderPath | Caminho para a pasta. Por exemplo: pasta/subpasta / |Sim |
| fileName | Especifique o nome do ficheiro no **folderPath** se pretender copiar a partir de um ficheiro específico. Se não for especificado qualquer valor para esta propriedade, o conjunto de dados de pontos a todos os ficheiros na pasta como origem. |Não |
| formato | Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formato](supported-file-formats-and-compression-codecs.md#orc-format), e [Parquet formato](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Nenhum (apenas para cenário de cópia binário) |
| Compressão | Especifique o tipo e o nível de compressão de dados. Para obter mais informações, consulte [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Optimal** e **Fastest**. |Não |

**Exemplo:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por HDFS origem e dependente.

### <a name="hdfs-as-source"></a>HDFS como origem

Para copiar dados de HDFS, defina o tipo de origem na atividade de cópia para **HdfsSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **HdfsSource** |Sim |
| Recursiva | Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada.<br/>Valores permitidos são: **verdadeiro** (predefinição), **false** | Não |
| distcpSettings | Grupo de propriedade ao utilizar o HDFS DistCp. | Não |
| resourceManagerEndpoint | O ponto final Yarn ResourceManager | Sim, se utilizar o DistCp |
| tempScriptPath | Um caminho de pasta utilizado para armazenar o script de comando DistCp temp. O ficheiro de script é gerado pelo ADF e será removido após a conclusão da tarefa de cópia. | Sim, se utilizar o DistCp |
| distcpOptions | Opções adicionais fornecidas ao comando DistCp. | Não |

**Exemplo: Origem do HDFS na atividade de cópia com o descarregamento**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Saiba mais sobre como utilizar o DistCp para copiar dados de HDFS eficiente da secção seguinte.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Utilizar o DistCp para copiar dados de HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) é um Hadoop de ferramenta de linha de comandos nativo para fazer a cópia distribuída num cluster de Hadoop. Quando executar um comando Distcp, primeiro lista todos os ficheiros para ser copiado, crie várias tarefas de mapa para o cluster do Hadoop e cada tarefa de mapa irá efetuar a cópia binária de origem para sink.

Copiar utilizando o DistCp para copiar ficheiros como o suporte de atividade-esteja no Blob do Azure (incluindo [testado cópia](copy-activity-performance.md) ou do Azure Data Lake Store, caso em que totalmente pode aproveitar energia do cluster em vez de executar no tempo de execução de integração Self-hosted . Este irá fornecer melhorar o débito de cópia, especialmente se o cluster for muito poderoso. Com base na sua configuração no Azure Data Factory, atividade de cópia automaticamente construir um comando distcp, submeter ao cluster do Hadoop e monitorizar o estado da cópia.

### <a name="prerequsites"></a>Prerequsites

Para utilizar o DistCp para copiar ficheiros como-é de HDFS ao Blob do Azure (incluindo cópia faseada) ou do Azure Data Lake Store, certifique-se o cluster de Hadoop cumpre abaixo requisitos:

1. Serviços de MapReduce e o Yarn estão ativados.
2. Versão do yarn é 2.5 ou superior.
3. Servidor do HDFS está integrado com o arquivo de dados de destino - BLOBs do Azure ou do Azure Data Lake Store:

    - Sistema de ficheiros de Blob do Azure é suportado nativamente desde 2.7 do Hadoop. Só tem de especificar o caminho de jar na configuração de env do Hadoop.
    - Sistema de ficheiros do Azure Data Lake Store é empacotado começando 3.0.0-alpha1 do Hadoop. Se o cluster de Hadoop é inferior ao que a versão, terá de importar manualmente ADLS relacionadas com jar pacotes (azure-datalake-store.jar) num cluster de [aqui](https://hadoop.apache.org/releases.html)e especifique o caminho de jar na configuração de env do Hadoop.

4. Prepare uma pasta temporária do HDFS. Esta pasta temporária é utilizada para armazenar o script de shell DistCp, pelo que vai ocupar espaço ao nível da KB.
5. Certifique-se a conta de utilizador fornecida no serviço ligado do HDFS tem permissão para um) submeter a aplicação no Yarn; b) tem permissão para criar subpasta, os ficheiros em acima pasta temporária de leitura/escrita.

### <a name="configurations"></a>Configurações

Abaixo está um exemplo de configuração de atividade de cópia para copiar dados de HDFS ao Blob do Azure utilizando o DistCp:

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilizar a autenticação Kerberos para o conector do HDFS

Existem duas opções para configurar o ambiente no local, de modo a utilizar a autenticação Kerberos no conector do HDFS. Pode escolher a melhor se adequa a maiúsculas e minúsculas.
* Opção 1: [máquina associação Self-hosted integração Runtime Kerberos realm](#kerberos-join-realm)
* Opção 2: [ativar a fidedignidade mútua entre o domínio do Windows e Kerberos realm](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: Associar a máquina de tempo de execução de integração de Self-hosted no Kerberos realm

#### <a name="requirements"></a>Requisitos

* A máquina de tempo de execução do Self-hosted integração tem de associar o realm Kerberos e não é possível associar a qualquer domínio Windows.

#### <a name="how-to-configure"></a>Como configurar

**Na máquina de tempo de execução do Self-hosted integração:**

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

#### <a name="requirements"></a>Requisitos

*   A máquina de tempo de execução do Self-hosted integração tem de associar um domínio Windows.
*   Necessita de permissão para atualizar as definições do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar

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

        ![Tipos de encriptação de configuração de Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Utilize **Ksetup** comandos para especificar o algoritmo de encriptação a utilizar sobre o REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e um principal de Kerberos, para poder utilizar o Kerberos principal no domínio Windows.

    1. Iniciar as ferramentas administrativas > **computadores e utilizadores do Active Directory**.

    2. Configurar funcionalidades avançadas clicando **vista** > **funcionalidades avançadas**.

    3. Localizar a conta à qual pretende criar mapeamentos e faça duplo clique para ver **mapeamentos de nome** > clique **Kerberos nomes** separador.

    4. Adicione um principal do realm.

        ![Identidade de segurança do mapa](media/connector-hdfs/map-security-identity.png)

**Na máquina de tempo de execução do Self-hosted integração:**

* Execute o seguinte **Ksetup** comandos para adicionar uma entrada do realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**De Azure Data Factory:**

* Configurar o conector do HDFS utilizando **autenticação do Windows** juntamente com a sua conta de domínio ou Principal de Kerberos para ligar à origem de dados de HDFS. Verifique [propriedades de serviço ligado do HDFS](#linked-service-properties) secção nos detalhes de configuração.


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).