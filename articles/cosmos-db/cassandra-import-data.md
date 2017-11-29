---
title: Importar dados de Cassandra para a base de dados do Azure Cosmos | Microsoft Docs
description: "Saiba como utilizar o comando CQL cópia para copiar dados Cassandra na base de dados do Azure Cosmos."
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 21168d0862cfdaaaced60fa80a2dc04859f49550
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Do Azure Cosmos DB: Dados de Cassandra de importação

Este tutorial fornece instruções sobre importar dados Cassandra para a BD do Cosmos Azure utilizando o comando de cópia de idioma de consulta Cassandra (CQL). 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Obter a cadeia de ligação
> * Importar dados utilizando o comando de cópia cqlsh
> * Importar através do conector do Spark 

# <a name="prerequisites"></a>Pré-requisitos

* Instalar [Apache Cassandra](http://cassandra.apache.org/download/) e especificamente Certifique-se *cqlsh* está presente.
* Aumentar o débito: durante a migração de dados depende da quantidade de débito aprovisionado para as suas tabelas. Lembre-se de que aumentar o débito para migrações de dados maior. Após concluir a migração, reduzir o débito para reduzir os custos. Para obter mais informações sobre como aumentar o débito no [portal do Azure](https://portal.azure.com), consulte [débito de conjunto para contentores de base de dados do Azure Cosmos](set-throughput.md).
* Ativar SSL: BD do Azure do Cosmos tem requisitos de segurança estritos e normas. Lembre-se de que ativar SSL quando se interage com a sua conta. Quando utilizar CQL com SSH, tem uma opção para fornecer informações de SSL. 

## <a name="find-your-connection-string"></a>Localizar a cadeia de ligação

1. No [portal do Azure](https://portal.azure.com), na extremidade esquerda, clique em **Azure Cosmos DB**.

2. No **subscrições** painel, selecione o nome da sua conta.

3. Clique em **cadeia de ligação**. Painel direito contém todas as informações que precisa de ligar com êxito à sua conta.

    ![Página de cadeia de ligação](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Utilizar cqlsh cópia

Para importar dados de Cassandra para base de dados do Azure Cosmos para utilização com a API de Cassandra, utilize as seguintes orientações:

1. Inicie sessão no cqhsh utilizando as informações de ligação do portal.
2. Utilize o [comandos de cópia de CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) para copiar dados locais para o ponto final de API do Apache Cassandra. Certifique-se de que a origem e destino estão no mesmo centro de dados minimizar os problemas de latência.

### <a name="guide-for-moving-data-with-cqlsh"></a>Guia para mover dados com cqlsh

1. Pré-criar e dimensionar a sua tabela:
    * Por predefinição, a base de dados do Azure Cosmos Aprovisiona uma nova API Cassandra tabela com 1000 unidades de pedido por segundo (RU/s) (com base em CQL criação está aprovisionada com 400 RU/s). Antes de começar a migração utilizando cqlsh, todas as suas tabelas de pré-criar o [portal do Azure](https://portal.azure.com) ou a partir de cqlsh. 

    * Do [portal do Azure](https://portal.azure.com), aumentar o débito das tabelas de débito predefinido (400 ou 1000 RU/s) para 10 000 RU/s durante a migração. Com o débito mais elevado, pode evitar limitação e migrar em menos tempo. Com a hora a hora de faturação do BD Azure Cosmos, pode reduzir o débito imediatamente após a migração para reduzir os custos.

2. Determine os encargos RU durante uma operação. Pode fazê-lo utilizando o SDK de API do Azure Cosmos DB Cassandra à sua escolha. Este exemplo mostra a versão do .NET de encargos de RU a obter. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Determine a latência do seu computador para o serviço de base de dados do Azure Cosmos. Se estiverem dentro de um centro de dados do Azure, a latência deve ser um número de milissegundos de baixa único dígito. Se estiver fora do Datacenter do Azure, em seguida, pode utilizar psping ou azurespeed.com para obter a latência aproximada da sua localização.   

4. Calcule os valores adequados para os parâmetros (NUMPROCESS, INGESTRATE, MAXBATCHSIZE ou MINBATCHSIZE) que fornecem um bom desempenho. 

5. Execute o comando de migração final. Execute este comando assume que foram iniciadas cqlsh utilizando as informações de cadeia de ligação.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Utilizar o Spark para importar dados

Para dados de um cluster existente em máquinas virtuais do Azure, importar dados de utilização do Spark também é opção exequível. Isto requer Spark configurar como intermediário, uma vez ou ingestão regular. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu como concluir as seguintes tarefas:

> [!div class="checklist"]
> * Obter a cadeia de ligação
> * Importar dados utilizando o comando de cópia de cql
> * Importar com o conector do Spark 

Agora pode avançar para a secção de conceitos para obter mais informações sobre a base de dados do Azure Cosmos. 

> [!div class="nextstepaction"]
>[Níveis de consistência sincronizáveis dados na base de dados do Azure Cosmos](../cosmos-db/consistency-levels.md)
