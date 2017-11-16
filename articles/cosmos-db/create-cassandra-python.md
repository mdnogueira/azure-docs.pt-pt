---
title: "Início rápido: Cassandra API com o Python - Cosmos BD do Azure | Microsoft Docs"
description: "Este guia de introdução mostra como utilizar Apache Cassandra API a BD Cosmos Azure para criar uma aplicação de perfil com o Python"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4ebc883e-c512-4e34-bd10-19f048661159
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: 95d58c600bc43059025e739a8cdb062287f4ffb5
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="quickstart-build-a-cassandra-app-with-python-and-azure-cosmos-db"></a>Início rápido: Criar uma aplicação de Cassandra com o Python e Azure Cosmos DB

Este guia de introdução mostra como utilizar o Python e a base de dados do Azure Cosmos [Cassandra API](cassandra-introduction.md) para criar uma perfil de aplicação através da clonagem de um exemplo do GitHub. Este guia de introdução também o orienta através da criação de uma conta de base de dados do Azure Cosmos através do portal do Azure baseada na web.

BD do Azure do Cosmos é serviço de base de dados com múltiplos modelo global distribuída da Microsoft. Pode criar e consultar documentos, tabela, chave-valor e bases de dados de gráfico, sendo todas beneficiam da distribuição global e as capacidades de dimensionamento horizontal o núcleo da BD do Cosmos Azure rapidamente.   

## <a name="prerequisites"></a>Pré-requisitos

* Antes de poder executar este exemplo, tem de ter os pré-requisitos seguintes:
    * [Python](https://www.python.org/downloads/) v2.7.14 de versão
    * [Git](http://git-scm.com/)
    * [Controlador de Python para Apache Cassandra](https://github.com/datastax/python-driver)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Em alternativa, pode [tente BD do Azure do Cosmos gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos.


## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documento, terá de criar uma conta de Cassandra com base de dados do Azure Cosmos.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clone uma API Cassandra aplicação a partir do github, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tais como o git bash e utilize o `cd` comando para alterar para uma pasta para instalar a aplicação de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender a forma como os recursos de base de dados são criados no código, pode rever os seguintes fragmentos. Os fragmentos são obtidos a partir do `pyquickstart.py` ficheiro. Caso contrário, pode avançar diretamente para [atualizar a cadeia de ligação](#update-your-connection-string). 

* Nome de utilizador e palavra-passe é definida utilizando a página de cadeia de ligação no portal do Azure. Substitua o path\to\cert com o caminho para o seu X509 certificado.

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* O `cluster` é inicializado com contactPoint informações. O contactPoint é obtido a partir do portal do Azure.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* O `cluster` estabelece ligação com o Azure Cosmos DB Cassandra API.

    ```python
    session = cluster.connect()
    ```

* É criado um novo keyspace.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* É criada uma nova tabela.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Entidades de chave/valor são introduzidas.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    batch = BatchStatement()
    batch.add(insert_data, (1, 'LyubovK', 'Dubai'))
    batch.add(insert_data, (2, 'JiriK', 'Toronto'))
    batch.add(insert_data, (3, 'IvanH', 'Mumbai'))
    batch.add(insert_data, (4, 'YuliaT', 'Seattle'))
    ....
    session.execute(batch)
    ```

* Consulta para obter obter todos os valores de chaves.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Consulta para obter um chave-valor.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite que a aplicação comunicar com a base de dados alojada.

1. No [portal do Azure](http://portal.azure.com/), clique em **cadeia de ligação**. 

    Utilize o ![Botão Copiar](./media/create-cassandra-python/copy.png) botão à direita do ecrã, para copiar o valor superior, o ponto de contacto.

    ![Ver e copiar um acesso nome, a palavra-passe e contacte ponto do utilizador no Azure painel do portal, ligação cadeia](./media/create-cassandra-python/keys.png)

2. Abra o ficheiro `config.py`. 

3. Colar o valor do ponto de contacto do portal através de `<FILLME>` linha 10.

    Linha 10 deve agora ter um aspeto semelhante a 

    `'contactPoint': 'cosmos-db-quickstarts.documents.azure.com:10350'`

4. Copie o valor de nome de utilizador a partir do portal e cole-o ao longo do `<FILLME>` linha 6.

    Linha 6 deve agora ter um aspeto semelhante a 

    `'username': 'cosmos-db-quickstart',`
    
5. Copie o valor de palavra-passe a partir do portal e cole-o ao longo do `<FILLME>` linha 8.

    Linha 8 deve agora ter um aspeto semelhante a

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Guarde o ficheiro config.py.
    
## <a name="use-the-x509-certificate"></a>Utilizar o X509 certificado

1. Se precisar de adicionar a entrada de Root da CyberTrust Baltimore, tem o número de série 02:00:00:b9 e SHA1 identificação digital d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Pode ser transferido a partir https://cacert.omniroot.com/bc2025.crt, guardados num ficheiro local com. cer de extensão

2. Abra pyquickstart.py e altere o 'path\to\cert' para apontar para o novo certificado.

3. Guarde pyquickstart.py.

## <a name="run-the-app"></a>Executar a aplicação

1. Utilize o comando cd git terminal para alterar para a pasta azure-cosmos-db-cassandra-python-getting-started. 

2. Execute os seguintes comandos para instalar os módulos necessários:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Execute o seguinte comando para iniciar a aplicação de nó:

    ```
    python pyquickstart.py
    ```

3. Verificar os resultados como esperado na linha de comandos.

    Prima CTRL + C para parar exection do programa e feche a janela de consola. 

    ![Ver e certifique-se a saída](./media/create-cassandra-python/output.png)
    
    Pode agora abrir o Explorador de dados no portal do Azure para ver a consulta, modificar e trabalhar com estes novos dados. 

    ![Ver os dados no Explorador de dados](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma coleção com o Data Explorer e a executar uma aplicação. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados de Cassandra para a base de dados do Azure Cosmos](cassandra-import-data.md)

