---
title: "Adicionar tolerância a falhas na atividade de cópia de fábrica de dados do Azure, a ignorar as linhas incompatíveis | Microsoft Docs"
description: "Saiba como adicionar tolerância a falhas na atividade de cópia de fábrica de dados do Azure por ignorar linhas incompatíveis durante a cópia"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5c32d4ac2c1179a83a82bd5deb41047b82e43b7e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Adicione tolerância a falhas na atividade de cópia ao ignorar linhas incompatíveis
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [tolerância a falhas na atividade de cópia do Data Factory versão 2](../copy-activity-fault-tolerance.md).

O Azure Data Factory [atividade de cópia](data-factory-data-movement-activities.md) oferece duas formas de lidar com linhas incompatíveis ao copiar dados entre os arquivos de dados de origem e dependente:

- Pode abortar e efetuar a cópia atividade quando os dados incompatíveis encontrado (comportamento predefinido).
- Pode continuar copiar todos os dados ao adicionar a tolerância a falhas e a ignorar as linhas de dados incompatíveis. Além disso, pode iniciar as linhas incompatíveis no Blob storage do Azure. Em seguida, pode examinar o registo para obter a causa da falha, corrija os dados na origem de dados e repita a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Atividade de cópia suporta três cenários para detetar, ignorar, dados e registo incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo sink**

    Por exemplo: copiar dados de um ficheiro CSV no armazenamento de BLOBs para base de dados SQL com uma definição de esquema contém três **INT** colunas de tipo. As linhas do ficheiro CSV que contêm dados numéricos, tais como `123,456,789` são copiados com êxito para o arquivo de sink. No entanto, as linhas que contêm valores não numéricos, tal como `123,456,abc` são detetados como incompatíveis e são ignorados.

- **Erro de correspondência no número de colunas entre a origem e o sink**

    Por exemplo: copiar dados de um ficheiro CSV no armazenamento de BLOBs para base de dados SQL com uma definição de esquema que contenha seis colunas. As linhas do ficheiro CSV que contenha seis colunas são copiadas com êxito para o arquivo de sink. As linhas do ficheiro CSV que contenha colunas mais ou menos de seis são detetadas como incompatíveis e são ignoradas.

- **Violação da chave primária ao escrever para uma base de dados relacional**

    Por exemplo: copiar dados do SQL server para uma base de dados do SQL Server. É definida uma chave primária na base de dados do SQL Server sink, mas essa nenhuma chave primária está definido no servidor SQL de origem. As linhas duplicadas que existe na origem não não possível copiar o sink. Atividade de cópia copia apenas a primeira linha dos dados de origem para o sink. As linhas subsequentes de origem que contêm o valor de chave primária duplicado são detetadas como incompatíveis e são ignoradas.

## <a name="configuration"></a>Configuração
O exemplo seguinte fornece uma definição de JSON para configurar a ignorar as linhas incompatíveis na atividade de cópia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Ative a ignorar incompatíveis linhas durante a cópia ou não. | Verdadeiro<br/>FALSE (predefinição) | Não |
| **redirectIncompatibleRowSettings** | Um grupo de propriedades que podem ser especificados quando pretender registar as linhas incompatíveis. | &nbsp; | Não |
| **linkedServiceName** | O serviço ligado do Storage do Azure para armazenar o registo que contém as linhas ignoradas. | O nome de um [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) ligado serviço, que se refere à instância de armazenamento que pretende utilizar para armazenar o ficheiro de registo. | Não |
| **caminho** | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique o caminho de armazenamento de BLOBs que pretende utilizar para registar os dados incompatíveis. Se não fornecer um caminho, o serviço cria um contentor para si. | Não |

## <a name="monitoring"></a>Monitorização
Após a conclusão da execução da atividade de cópia, pode ver o número de linhas ignorados na secção monitorização:

![Monitor ignorada linhas incompatíveis](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Se configurar para registar as linhas incompatíveis, pode encontrar o ficheiro de registo neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` no ficheiro de registo, pode ver as linhas que foram ignoradas e a causa raiz de incompatibilidade.

Os dados originais e o erro correspondente são registadas no ficheiro. Um exemplo do conteúdo do ficheiro de registo é o seguinte:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a atividade de cópia de fábrica de dados do Azure, consulte [mover dados utilizando a atividade de cópia](data-factory-data-movement-activities.md).