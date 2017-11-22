---
title: "Criar uma base de dados de gráficos do Azure Cosmos DB com Java | Microsoft Docs"
description: "Apresenta um exemplo de código Java que pode utilizar para ligar e consultar dados de gráficos no Azure Cosmos DB com Gremlin."
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: denlee
ms.openlocfilehash: 84a9ae4a48e7e71d70214550dd203a0468a31de6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="azure-cosmos-db-create-a-graph-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Criar uma base de dados de gráficos com Java e o portal do Azure

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Utilizar base de dados do Azure Cosmos, pode rapidamente criar e consultar documento gerido, tabela e bases de dados do gráfico. 

Este guia de introdução cria uma base de dados do gráfico simples utilizando as ferramentas do portais do Azure para a base de dados do Azure Cosmos. Este guia de início rápido também lhe mostra como criar rapidamente uma aplicação de consola Java com o controlador [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver). As instruções deste guia de início rápido podem ser seguidas em qualquer sistema operativo capaz de executar Java. Este guia de introdução familiarizes, com a criação e modificação de gráficos na IU ou através de programação, optando-se a sua preferência. 

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Além disso:

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Transferir](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um arquivo binário [Maven](http://maven.apache.org/)
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de gráficos, tem de criar uma conta de base de dados do Gremlin (Gráfico) com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados de gráfico. 

1. Clique em **Explorador de dados** > **gráfico novo**.

    O **adicionar gráfico** área é apresentada na extremidade direita, poderá ter de se deslocar para a direita para vê-lo.

    ![O Explorador de dados, a página de adicionar gráfico do portal do Azure](./media/create-graph-java/azure-cosmosdb-data-explorer-graph.png)

2. No **adicionar gráfico** página, introduza as definições para o novo gráfico.

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID da base de dados|base de dados de exemplo|Introduza *base de dados de exemplo* como o nome para a nova base de dados. Os nomes das bases de dados têm de ter entre um e 255 carateres e não podem conter `/ \ # ?` nem espaços à direita.
    ID do Graph|gráfico de exemplo|Introduza *exemplo gráfico* como o nome para a nova coleção. Os nomes de gráfico têm os mesmos requisitos de caráter como IDs da base de dados.
    Capacidade de Armazenamento|Fixa (10 GB)|Altere o valor para **fixo (10 GB)**. Este valor é a capacidade de armazenamento da base de dados.
    Débito|400 RUs|Altere o débito para 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.
    Chave de partição|Deixar em branco|Para fins deste guia de início rápido, deixe a chave de partição em branco.

3. Assim que o formulário estiver preenchido, clique em **OK**.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos de clone de uma aplicação da Graph API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente.  

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, tais como o git bash e utilize o `cd` comando para alterar para uma pasta para instalar a aplicação de exemplo.  

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender a forma como os recursos de base de dados são criados no código, pode rever os seguintes fragmentos. Os fragmentos são obtidos a partir do `Program.java` ficheiro na pasta C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted. Caso contrário, pode avançar diretamente para [atualizar a cadeia de ligação](#update-your-connection-information). 

* O Gremlin `Client` é inicializado na configuração em `src/remote.yaml`.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* São executados uma série de passos do Gremlin com o método `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Atualize as informações de ligação

Agora, regresse ao portal do Azure para obter as informações de ligação e copie-o para a aplicação. Estas definições de ativam a sua aplicação comunicar com a base de dados alojada.

1. No [portal do Azure](http://portal.azure.com/), clique em **chaves**. 

    Copie a primeira parte do valor URI.

    ![Ver e copiar uma chave de acesso na página do portal, as chaves do Azure](./media/create-graph-java/keys.png)
2. Abra o ficheiro de src/remote.yaml e colar o valor ao longo do `$name$` no `hosts: [$name$.graphs.azure.com]`.

    Linha 1 do remote.yaml deve agora ter um aspeto semelhante a 

    `hosts: [test-graph.graphs.azure.com]`

3. No portal do Azure, utilize o botão Copiar para copiar a chave primária e cole-o ao longo do `$masterKey$` no `password: $masterKey$`.

    Linha 4 de remote.yaml deve agora ter um aspeto semelhante a 

    `password: 2Ggkr662ifxz2Mg==`

4. Alterar linha 3 de remote.yaml do

    `username: /dbs/$database$/colls/$collection$`

    para 

    `username: /dbs/sample-database/colls/sample-graph`

5. Guarde o ficheiro remote.yaml.

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Na janela de terminal do git, `cd` na pasta azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Na janela de terminal do git, utilize o seguinte comando para instalar os pacotes necessários do Java.

   ```
   mvn package
   ```

3. Na janela de terminal do git, utilize o seguinte comando para iniciar a aplicação de Java.
    
    ```
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    A janela de terminal apresenta os vértices a adicionar ao gráfico. 
    
    Se ocorrer erros de tempo limite, verifique que atualizadas as informações de ligação corretamente em [atualizar as suas informações de ligação](#update-your-connection-information)e também tentar executar o último comando novamente. 
    
    Depois do programa é interrompido, prima Enter, mude novamente para o portal do Azure no seu browser da internet. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Rever e adicionar dados de exemplo

Agora, pode voltar ao Data Explorer e ver os vértices adicionados ao gráfico e adicionar mais pontos de dados.

1. Clique em **Explorador de dados**, expanda **exemplo gráfico**, clique em **gráfico**e, em seguida, clique em **aplicar filtro**. 

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. Na lista **Resultados**, repare nos utilizadores novos que são adicionados ao gráfico. Selecione **ben** e repare que está ligado a robin. Pode mover vértices à volta ao arrastar e largar, ampliar e reduzir por deslocar a roda do rato e expandir o tamanho do gráfico com a seta dupla. 

   ![Vértices novos no gráfico no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Vamos adicionar alguns novos utilizadores. Clique no botão **Vértice Novo** para adicionar os dados ao gráfico.

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Introduza uma etiqueta de *pessoa*.

5. Clique em **Adicionar propriedade** para adicionar cada uma das seguintes propriedades. Tenha em atenção que pode criar propriedades exclusivas para cada pessoa no seu gráfico. Só é necessária a chave de id.

    key|valor|Notas
    ----|----|----
    ID|ashley|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|feminino| 
    técnico | java | 

    > [!NOTE]
    > Neste guia de início rápido, criámos uma coleção não particionada. No entanto, se criar uma coleção particionada mediante a especificação de uma chave de partição durante a criação da coleção, terá de incluir a chave da partição como uma chave em cada vértice novo. 

6. Clique em **OK**. Poderá ter de expandir o ecrã para ver **OK**, na parte inferior.

7. Clique em **Vértice Novo** novamente e adicione outro utilizador. 

8. Introduza uma etiqueta de *pessoa*.

9. Clique em **Adicionar propriedade** para adicionar cada uma das seguintes propriedades:

    key|valor|Notas
    ----|----|----
    ID|rakesh|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|masculino| 
    escola|MIT| 

10. Clique em **OK**. 

11. Clique em de **aplicar filtro** botão com a predefinição `g.V()` filtro para apresentar todos os valores no gráfico. Todos os utilizadores aparecem agora na lista **Resultados**. 

    À medida que adiciona mais dados, pode utilizar filtros para limitar os resultados. Por predefinição, o Explorador de dados utiliza `g.V()` obter todos os vértices num gráfico. Pode alterá-la para outro [consulta gráfico](tutorial-query-graph.md), tais como `g.V().count()`, para devolver uma contagem de todos os vértices no gráfico no formato JSON. Se tiver alterado o filtro, altere o filtro de volta para `g.V()` e clique em **aplicar filtro** para apresentar todos os resultados novamente.

12. Agora, podemos ligar rakesh e ashley. Certifique-se **ashley** está selecionado no **resultados** lista, em seguida, clique no botão Editar junto a **destinos** no inferior direita. Poderá ter de alargar a janela para ver a área **Propriedades**.

   ![Alterar o destino de um vértice de um gráfico](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. No **destino** caixa tipo *rakesh*e no **etiqueta Edge** caixa tipo *sabe*e, em seguida, clique na verificação.

   ![Adicionar uma ligação entre ashley e rakesh no Data Explorer](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. Agora, selecione **rakesh** na lista de resultados e veja se ashley e rakesh estão ligados. 

   ![Dois vértices ligados no Data Explorer](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

   Que conclui a parte da criação de recursos deste tutorial. Pode continuar a adicionar vertexes ao seu gráfico, modifique os vertexes existentes ou alterar as consultas. Agora vamos rever as métricas de base de dados do Azure Cosmos fornece e, em seguida, limpar os recursos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)

