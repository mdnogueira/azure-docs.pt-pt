---
title: "Mover dados utilizando a atividade de cópia | Microsoft Docs"
description: "Saiba mais sobre o movimento de dados no Data Factory pipelines: migração de dados entre os arquivos de nuvem e entre um arquivo no local e um arquivo de nuvem. Utilize a atividade de cópia."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d6cddf54827d44e41add9e1e6b1a4323e03879e7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="move-data-by-using-copy-activity"></a>Mover dados utilizando a atividade de cópia
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-data-movement-activities.md)
> * [Versão 2 - Pré-visualização](../copy-activity-overview.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [atividade de cópia na V2](../copy-activity-overview.md).

## <a name="overview"></a>Descrição geral
No Azure Data Factory, pode utilizar a atividade de cópia para copiar dados entre no local e nuvem arquivos de dados. Depois dos dados são copiados, este pode ser mais transformado e analisado. Também pode utilizar a atividade de cópia de publicação de transformação e resultados de análise para intelligence de negócio (BI) e o consumo de aplicação.

![Função de atividade de cópia](media/data-factory-data-movement-activities/copy-activity.png)

Atividade de cópia tem a alimentação ligada por segura, fiável e escalável, e [serviço globalmente disponível](#global). Este artigo fornece detalhes sobre o movimento de dados no Data Factory e a atividade de cópia.

Em primeiro lugar, vamos ver como a migração de dados ocorre entre dois arquivos de dados de nuvem e entre um arquivo de dados no local e um arquivo de dados na nuvem.

> [!NOTE]
> Para saber mais sobre as atividades em geral, consulte [compreender pipelines e as atividades](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiar dados entre dois arquivos de dados de nuvem
Quando os arquivos de dados de origem e dependente estão na nuvem, atividade de cópia realiza as seguintes fases para copiar dados da origem para o sink. O serviço que está na base de atividade de cópia:

1. Lê os dados do arquivo de dados de origem.
2. Efetua a serialização/desserialização, compressão/descompressão, mapeamento de colunas e a conversão do tipo. Faz estas operações com base nas configurações do conjunto de dados de entrada, o conjunto de dados de saída e a atividade de cópia.
3. Escreve dados para o arquivo de dados de destino.

O serviço escolhe automaticamente a região ideal para efetuar o movimento de dados. Esta região é, normalmente, aquele mais próximo para o arquivo de dados do sink.

![Cópia na nuvem para a nuvem](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um arquivo de dados no local e um arquivo de dados na nuvem
Em segurança mover dados entre um arquivo de dados no local e um arquivo de dados na nuvem, para instalar o Data Management Gateway no seu computador local. O Data Management Gateway é um agente que permite o movimento de dados de híbrida e de processamento. Pode instalá-lo no mesmo computador como os dados de arquivo em si ou num computador separado que tenha acesso ao arquivo de dados.

Neste cenário, o Data Management Gateway efetua a serialização/desserialização, a compressão/descompressão, a coluna de mapeamento e a conversão do tipo. Não do fluxo de dados através do serviço do Azure Data Factory. Em vez disso, o Data Management Gateway diretamente escreve os dados para o arquivo de destino.

![Cópia no local-para-nuvem](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Consulte [mover dados entre no local e na nuvem arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) para uma introdução e uma explicação passo a passo. Consulte [Data Management Gateway](data-factory-data-management-gateway.md) para obter informações detalhadas sobre este agente.

Também pode mover dados de/para suportada arquivos de dados que estão alojados em máquinas virtuais (VMs) IaaS do Azure utilizando o Data Management Gateway. Neste caso, pode instalar o Data Management Gateway na mesma VM, como os dados de arquivo em si ou numa VM separada que tenha acesso ao arquivo de dados.

## <a name="supported-data-stores-and-formats"></a>Arquivos de dados suportadas e formatos
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

> [!NOTE] 
> Se precisar de mover dados de/para um arquivo de dados que a Atividade de Cópia não suporte ou de utilizar uma **atividade personalizada** na Fábrica de Dados com a sua própria lógica para copiar/mover dados. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="supported-file-formats"></a>Formatos de ficheiros suportados
Pode utilizar a atividade de cópia para **copiar ficheiros como-é** entre dois baseados em ficheiros arquivos de dados, pode ignorar o [Formatar secção](data-factory-create-datasets.md) em ambas as definições do conjunto de dados de entrada e de saída. Os dados são copiados de forma eficiente sem qualquer serialização/desserialização.

Atividade de cópia também lê a partir de e escreve os ficheiros em formatos especificados: **texto, JSON, Avro, ORC e Parquet**e codec de compressão **GZip, Deflate, BZip2 e ZipDeflate** são suportados. Consulte [suportado os formatos de ficheiro e compressão](data-factory-supported-file-and-compression-formats.md) com detalhes.

Por exemplo, pode efetuar as seguintes atividades de cópia:

* Copiar os dados no SQL Server no local e escrita ao Azure Data Lake Store no formato ORC.
* Copiar os ficheiros no formato de texto (CSV) do sistema de ficheiros no local e escrita aos BLOBs do Azure no formato Avro.
* Copiar ficheiros zipped de sistema de ficheiros no local e, em seguida, descomprimir telefone ao Azure Data Lake Store.
* Copiar os dados no formato de texto comprimidos (CSV) de GZip Blob do Azure e de escrita para a SQL Database do Azure.

## <a name="global"></a>Movimento de dados globalmente disponível
O Azure Data Factory está disponível apenas nas regiões EUA oeste, EUA leste e Europa do Norte. No entanto, o serviço que está na base de atividade de cópia está disponível globalmente nas seguintes regiões e localizações geográficas. A topologia globalmente disponível garante o movimento de dados eficiente que normalmente evita saltos por várias regiões. Consulte [serviços por região](https://azure.microsoft.com/regions/#services) para disponibilidade da fábrica de dados e o movimento de dados numa região.

### <a name="copy-data-between-cloud-data-stores"></a>Copiar dados entre os arquivos de dados de nuvem
Quando os arquivos de dados de origem e dependente estão na nuvem, a fábrica de dados utiliza uma implementação de serviço na região mais próxima o sink da mesma geografia para mover os dados. Veja os mapeamentos na tabela seguinte:

| Geografia dos arquivos de dados de destino | Região do arquivo de dados de destino | Região utilizada para o movimento de dados |
|:--- |:--- |:--- |
| Estados Unidos | EUA Leste | EUA Leste |
| &nbsp; | EUA Leste 2 | EUA Leste 2 |
| &nbsp; | EUA Central | EUA Central |
| &nbsp; | EUA Centro-Norte | EUA Centro-Norte |
| &nbsp; | EUA Centro-Sul | EUA Centro-Sul |
| &nbsp; | EUA Centro-Oeste | EUA Centro-Oeste |
| &nbsp; | EUA Oeste | EUA Oeste |
| &nbsp; | EUA Oeste 2 | EUA Oeste |
| Canadá | Leste do Canadá | Canadá Central |
| &nbsp; | Canadá Central | Canadá Central |
| Brasil | Sul do Brasil | Sul do Brasil |
| Europa | Europa do Norte | Europa do Norte |
| &nbsp; | Europa Ocidental | Europa Ocidental |
| Reino Unido | Reino Unido Oeste | Reino Unido Sul |
| &nbsp; | Reino Unido Sul | Reino Unido Sul |
| Ásia-Pacífico | Sudeste Asiático | Sudeste Asiático |
| &nbsp; | Ásia Oriental | Sudeste Asiático |
| Austrália | Leste da Austrália | Leste da Austrália |
| &nbsp; | Sudeste da Austrália | Sudeste da Austrália |
| Índia | Índia Central | Índia Central |
| &nbsp; | Índia Ocidental | Índia Central |
| &nbsp; | Sul da Índia | Índia Central |
| Japão | Leste do Japão | Leste do Japão |
| &nbsp; | Oeste do Japão | Leste do Japão |
| Coreia | Coreia Central | Coreia Central |
| &nbsp; | Coreia do Sul | Coreia Central |

Em alternativa, pode indicar explicitamente a região do serviço do Data Factory para ser utilizado para efetuar a cópia especificando `executionLocation` propriedade de atividade de cópia `typeProperties`. Os valores suportados para esta propriedade constam acima **região utilizada para o movimento de dados** coluna. Tenha em atenção de que os seus dados atravessa nessa região através da transmissão durante a cópia. Por exemplo, para copiar entre Azure armazena na Coreia, pode especificar `"executionLocation": "Japan East"` para encaminhar através de região Japão (consulte [JSON de exemplo](#by-using-json-scripts) como referência).

> [!NOTE]
> Se a região de destino do arquivo de dados não se encontra na lista precedente ou undetectable, por predefinição atividade de cópia falhará em vez de percorrer uma região alternativa, a menos que `executionLocation` está especificado. A lista de região suportada será expandida ao longo do tempo.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um arquivo de dados no local e um arquivo de dados na nuvem
Quando os dados estão a ser copiados no local (ou máquinas virtuais/IaaS do Azure) e na nuvem arquivos, [Data Management Gateway](data-factory-data-management-gateway.md) realiza o movimento de dados num computador local ou máquina virtual. Os não do fluxo de dados através do serviço na nuvem, a menos que utilize o [testado cópia](data-factory-copy-activity-performance.md#staged-copy) capacidade. Neste caso, os dados fluem através de transição Blob storage do Azure antes do que é escrito para o arquivo de dados do sink.

## <a name="create-a-pipeline-with-copy-activity"></a>Criar um pipeline com atividade de cópia
Pode criar um pipeline com atividade de cópia de algumas formas:

### <a name="by-using-the-copy-wizard"></a>Utilizando o Assistente de cópia
O Assistente de cópia de fábrica de dados ajuda-o a criar um pipeline com atividade de cópia. Este pipeline permite-lhe copiar dados de fontes suportadas para destinos *sem ter de escrever JSON* definições para os serviços ligados, conjuntos de dados e pipelines. Consulte [Assistente de cópia do Data Factory](data-factory-copy-wizard.md) para obter detalhes sobre o assistente.  

### <a name="by-using-json-scripts"></a>Ao utilizar scripts JSON
Pode utilizar o Editor do Data Factory no portal do Azure, no Visual Studio ou no Azure PowerShell para criar uma definição de JSON para um pipeline (utilizando a atividade de cópia). Em seguida, pode implementá-la para criar o pipeline na fábrica de dados. Consulte [Tutorial: Utilize a atividade de cópia no pipeline Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para um tutorial com instruções passo a passo.    

As propriedades de JSON (por exemplo, nome, descrição e de saída, tabelas e as políticas) estão disponíveis para todos os tipos de atividades. Propriedades que estão disponíveis no `typeProperties` secção da atividade variar de acordo com cada tipo de atividade.

Para a atividade de cópia, o `typeProperties` secção varia consoante os tipos de origens e sinks. Clique em origem/sink no [suportados origens e sinks](#supported-data-stores-and-formats) secção para saber mais sobre as propriedades de tipo que suporta a atividade de cópia para este arquivo de dados.

Segue-se uma definição de JSON de exemplo:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
A agenda que é definida no conjunto de dados de saída determina quando a atividade é executada (por exemplo: **diária**, frequência como **dia**e o intervalo como **1**). A atividade copia dados a partir de um conjunto de dados de entrada (**origem**) para um conjunto de dados de saída (**sink**).

Pode especificar mais do que um conjunto de dados de entrada para a atividade de cópia. São utilizados para verificar as dependências antes da atividade é executada. No entanto, apenas os dados do primeiro conjunto de dados são copiados para o conjunto de dados de destino. Para obter mais informações, consulte [agendamento e execução](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte o [guia Otimização e de desempenho de atividade de cópia](data-factory-copy-activity-performance.md), que descreve os principais fatores que afetam o desempenho do movimento de dados (atividade de cópia) no Azure Data Factory. Também apresenta uma lista de desempenho durante os testes internos e descreve as várias formas para otimizar o desempenho de atividade de cópia.

## <a name="fault-tolerance"></a>Tolerância a falhas
Por predefinição, a atividade de cópia irá parar copiar dados e devolver falha quando encontrar dados incompatíveis entre a origem e dependente; Embora possa configurar explicitamente ignorar e iniciar a linhas incompatíveis e apenas copiar esses dados compatíveis para fazer a cópia foi concluída com êxito. Consulte o [tolerância a falhas de atividade de cópia](data-factory-copy-activity-fault-tolerance.md) em obter mais detalhes.

## <a name="security-considerations"></a>Considerações de segurança
Consulte o [considerações de segurança](data-factory-data-movement-security-considerations.md), que descreve a infraestrutura de segurança que utilizam serviços de movimento de dados no Azure Data Factory para proteger os seus dados.

## <a name="scheduling-and-sequential-copy"></a>Cópia de agendamento e sequencial
Consulte [agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações detalhadas sobre como funciona o agendamento e execução fábrica de dados. É possível executar várias operações de cópia umas a seguir de forma sequenciais/ordenadas. Consulte o [copiar sequencialmente](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) secção.

## <a name="type-conversions"></a>Conversões de tipo
Arquivos de dados diferentes têm sistemas de tipo nativo diferente. Atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de dois passos:

1. Converter de tipos nativa de origem para um tipo .NET.
2. Converter de um tipo .NET para o tipo de sink nativo.

O mapeamento de um sistema de tipo nativo para um tipo .NET para um arquivo de dados está no artigo de arquivo de dados correspondentes. (Clique na ligação específica no [arquivos de dados suportados](#supported-data-stores) tabela). Pode utilizar estes mapeamentos para determinar tipos adequados ao criar as tabelas, de modo a atividade de cópia realiza os conversões à direita.

## <a name="next-steps"></a>Passos seguintes
* Para mais informações sobre a atividade de cópia, consulte [copiar dados do Blob storage do Azure para a SQL Database do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para saber mais sobre como mover dados a partir de um arquivo de dados no local para um arquivo de dados na nuvem, consulte [mover os dados no local para os arquivos de dados de nuvem](data-factory-move-data-between-onprem-and-cloud.md).
