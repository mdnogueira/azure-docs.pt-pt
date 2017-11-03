---
title: Utilize o mongoimport e mongorestore com a API de BD do Cosmos do Azure para o MongoDB | Microsoft Docs
description: Saiba como utilizar mongoimport e mongorestore para importar dados a uma API para a conta do MongoDB
keywords: mongoimport, mongorestore
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Do Azure Cosmos DB: Dados de MongoDB de importação 

Para migrar dados de MongoDB a uma conta de base de dados do Azure Cosmos para utilização com a API para o MongoDB, tem de:

* Transferir o *mongoimport.exe* ou *mongorestore.exe* do [Centro de transferências do MongoDB](https://www.mongodb.com/download-center).
* Obter o [API de cadeia de ligação do MongoDB](connect-mongodb-account.md).

Se estiver a importar dados de MongoDB e planeie a utilizá-la com a base de dados do Cosmos do Azure, deve utilizar o [ferramenta de migração de dados](import-data.md) para importar dados.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Obter a cadeia de ligação
> * Importar dados de MongoDB utilizando mongoimport
> * Importar dados de MongoDB utilizando mongorestore

## <a name="prerequisites"></a>Pré-requisitos

* Aumentar o débito: durante a migração de dados depende da quantidade de débito que configurou para as suas coleções. Lembre-se de que aumentar o débito para migrações de dados maior. Após concluir a migração, reduzir o débito para reduzir os custos. Para obter mais informações sobre como aumentar o débito no [portal do Azure](https://portal.azure.com), consulte [níveis de desempenho e escalões de preço do BD Azure Cosmos](performance-levels.md).

* Ativar SSL: BD do Azure do Cosmos tem requisitos de segurança estritos e normas. Lembre-se de que ativar SSL quando se interage com a sua conta. Os procedimentos no resto do artigo incluem como ativar SSL para mongoimport e mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Localizar as informações de cadeia de ligação (anfitrião, porta, nome de utilizador e palavra-passe)

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, clique em de **Azure Cosmos DB** entrada.
2. No **subscrições** painel, selecione o nome da sua conta.
3. No **cadeia de ligação** painel, clique em **cadeia de ligação**.  
Painel direito contém todas as informações que precisa de ligar com êxito à sua conta.

    ![Painel de cadeia de ligação](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Importar dados para a API para o MongoDB utilizando mongoimport

Para importar dados para a sua conta de base de dados do Azure Cosmos, utilize o modelo seguinte. Preencha *anfitrião*, *username*, e *palavra-passe* com os valores que são específicos para a sua conta.  

Modelo:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exemplo:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Importar dados para a API para o MongoDB utilizando mongorestore

Para restaurar dados para a sua API para a conta do MongoDB, utilize o modelo seguinte para executar a importação. Preencha *anfitrião*, *username*, e *palavra-passe* com os valores que são específicos para a sua conta.

Modelo:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exemplo:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Guia para uma migração com êxito

1. Pré-criar e dimensionar as coleções:
        
    * Por predefinição, a base de dados do Azure Cosmos Aprovisiona uma nova coleção de MongoDB com 1000 unidades de pedido (RUs). Antes de começar a migração utilizando mongoimport, mongorestore ou mongomirror, todas as suas coleções de pré-criar o [portal do Azure](https://portal.azure.com) ou a partir de ferramentas e controladores de MongoDB. Se a coleção é superior a 10 GB, certifique-se criar um [coleção em partição horizontal/particionada](partition-data.md) com uma chave de partição horizontal adequado.

    * Do [portal do Azure](https://portal.azure.com), aumentar o débito das coleções de 1.000 RUs para uma coleção de partições únicas e 2500 RUs para uma coleção a apenas para a migração. Com o débito mais elevado, pode evitar limitação e migrar em menos tempo. Com a hora a hora de faturação do BD Azure Cosmos, pode reduzir o débito imediatamente após a migração para reduzir os custos.

2. Calcular a taxa RU aproximada escrita um único documento:

    a. Ligar à base de dados MongoDB de BD do Cosmos Azure a partir da Shell do MongoDB. Pode encontrar instruções na [ligar uma aplicação do MongoDB à base de dados do Azure Cosmos](connect-mongodb-account.md).
    
    b. Execute um comando de inserção de exemplo utilizando um dos seus documentos de exemplo a partir da Shell do MongoDB:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Executar ```db.runCommand({getLastRequestStatistics: 1})``` e receberá uma resposta como esta:
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. Tome nota da taxa de pedidos.
    
3. Determine a latência do seu computador para o serviço de nuvem do Azure Cosmos DB:
    
    a. Ative o registo verboso da Shell do MongoDB utilizando este comando:```setVerboseShell(true)```
    
    b. Executar uma consulta simple na base de dados: ```db.coll.find().limit(1)```. Irá receber uma resposta como esta:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Remova o documento inserido antes da migração para se certificar de que existem não existem documentos duplicados. Pode remover documentos ao utilizar este comando:```db.coll.remove({})```

5. Calcular o aproximado *batchSize* e *numInsertionWorkers* valores:

    * Para *batchSize*, divide o total aprovisionado RUs por RUs consumidos da sua escrita único documento no passo 3.
    
    * Se o calculada *batchSize* < = 24, utilizar esse número como seu *batchSize* valor.
    
    * Se o calculada *batchSize* > 24, defina o *batchSize* valor e 24.
    
    * Para *numInsertionWorkers*, utilize este equação: *numInsertionWorkers = (débito aprovisionado * latência em segundos) / (tamanho do lote * consumido RUs para uma única escrita)*.
        
    |Propriedade|Valor|
    |--------|-----|
    |batchSize| 24 |
    |RUs aprovisionados | 10000 |
    |Latência | 0.100 s |
    |RU cobrada escrita 1 documento | 10 RUs |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RUs x 0.1 s) / (24 x 10 RUs) = 4.1666*

6. Execute o comando de migração final:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Passos seguintes

Pode avançar para o próximo tutorial e saiba como consultar dados de MongoDB, utilizando o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar dados de MongoDB?](../cosmos-db/tutorial-query-mongodb.md)
