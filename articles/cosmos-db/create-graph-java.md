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
ms.topic: hero-article
ms.date: 08/07/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: afa4fe6cdef298e4504ddcf3e344ee6a5c181653
ms.contentlocale: pt-pt
ms.lasthandoff: 08/08/2017

---
# <a name="azure-cosmos-db-create-a-graph-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Criar uma base de dados de gráficos com Java e o portal do Azure

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de início rápido cria uma base de dados de gráficos com as ferramentas do portal do Azure para o Azure Cosmos DB. Este guia de início rápido também lhe mostra como criar rapidamente uma aplicação de consola Java com o controlador [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver). As instruções deste guia de início rápido podem ser seguidas em qualquer sistema operativo capaz de executar Java. Quando concluir este guia, estará familiarizado com a criação e modificação de recursos de gráficos na IU ou programaticamente, conforme a sua preferência. 

## <a name="prerequisites"></a>Pré-requisitos

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Transferir](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um arquivo binário [Maven](http://maven.apache.org/)
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de gráficos, tem de criar uma conta de base de dados do Gremlin (Gráfico) com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados de gráfico. 

1. No portal do Azure, no menu de navegação à esquerda, clique em **Data Explorer (Pré-visualização)**. 
2. No painel **Data Explorer (Pré-visualização)**, clique em **Novo Gráfico** e preencha a página com as informações seguintes.

    ![Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-data-explorer.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID da base de dados|base de dados de exemplo|O ID da base de dados nova. Os nomes das bases de dados têm de ter entre um e 255 carateres e não podem conter `/ \ # ?` nem espaços à direita.
    ID do Graph|gráfico de exemplo|O ID do seu novo gráfico. Os nomes dos gráficos têm os mesmos requisitos de carateres que os IDs das bases de dados.
    Capacidade de Armazenamento| 10 GB|Deixe o valor predefinido. Esta é a capacidade de armazenamento da base de dados.
    Débito|400 RUs|Deixe o valor predefinido. Pode aumentar verticalmente o débito mais tarde, se quiser reduzir a latência.
    RU/m|Desativado|Deixe o valor predefinido. Se tiver de lidar mais tarde com grandes cargas de trabalho, pode ativar a funcionalidade [RU/m](request-units-per-minute.md) nesse momento.
    Chave de partição|Deixar em branco|Para fins deste guia de início rápido, deixe a chave de partição em branco.

3. Assim que o formulário estiver preenchido, clique em **OK**.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de gráfico a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o ficheiro `Program.java` na pasta \src\GetStarted e localize estas linhas de código. 

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

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

1. Abra o ficheiro src/remote.yaml. 

3. Preencha os valores *anfitriões*, *nome de utilizar* e *palavra-passe* valores no ficheiro src/remote.yaml. Não é necessário alterar as restantes definições.

    Definição|Valor sugerido|Descrição
    ---|---|---
    Anfitriões|[***.graphs.azure.com]|Veja a captura de ecrã a seguir a esta tabela. Este é o valor de Gremlin URI na página Descrição Geral do portal do Azure, entre parênteses, com :443/ à direita removido.<br><br>Este valor também pode ser obtido a partir do separador Chaves, com o valor do URI, removendo https://, alterando os documentos para gráficos e removendo :443/ à direita.
    Nome de utilizador|/dbs/sample-database/colls/sample-graph|O recurso do formulário `/dbs/<db>/colls/<coll>`, em que `<db>` é o nome da base de dados existente e `<coll>` é o nome da coleção existente.
    Palavra-passe|*A chave mestra principal*|Veja a segunda captura de ecrã a seguir a esta tabela. Este valor é a chave primária, que pode ser obtida na página Chaves do portal do Azure, na caixa Chave Primária. Copie o valor com o botão Copiar, no lado direito da caixa.

    No valor Anfitriões, copie o valor de **URI do Gremlin** da página **Descrição Geral**. Se estiver vazia, veja as instruções na linha Anfitriões da tabela anterior relativamente à criação do URI do Gremlin a partir do painel Chaves.
![Ver e copiar o valor do URI do Gremlin na página Descrição Geral no portal do Azure](./media/create-graph-java/gremlin-uri.png)

    No valor Palavra-passe, copie a **Chave Primária** a partir do painel **Chaves**: ![Ver e copiar a chave primária no portal do Azure, página Chaves](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Na janela de terminal do git, `cd` na pasta azure-cosmos-db-graph-java-getting-started.

2. Na janela de terminal do git, escreva `mvn package` para instalar os pacotes Java necessários.

3. Na janela de terminal do git, execute `mvn exec:java -D exec.mainClass=GetStarted.Program` na janela de terminal para iniciar a aplicação de Java.

A janela de terminal apresenta os vértices a adicionar ao gráfico. Após a conclusão do programa, mude novamente para o portal do Azure no browser. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Rever e adicionar dados de exemplo

Agora, pode voltar ao Data Explorer e ver os vértices adicionados ao gráfico e adicionar mais pontos de dados.

1. No Data Explorer, expanda **sample-database**/**sample-graph**, clique em **Gráfico** e clique em **Aplicar Filtro**. 

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. Na lista **Resultados**, repare nos utilizadores novos que são adicionados ao gráfico. Selecione **ben** e repare que está ligado a robin. Pode mover os vértices ao longo do explorador do gráfico, ampliar e reduzir e expandir o tamanho da superfície do explorador do gráfico. 

   ![Vértices novos no gráfico no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Vamos utilizar o Data Explorer para adicionar alguns utilizadores novos. Clique no botão **Vértice Novo** para adicionar os dados ao gráfico.

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Introduza a etiqueta *pessoa* e introduza as chaves e os valores seguintes, para criar o primeiro vértice do gráfico. Tenha em atenção que pode criar propriedades exclusivas para cada pessoa no seu gráfico. Só é necessária a chave de id.

    key|valor|Notas
    ----|----|----
    ID|ashley|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|feminino| 
    técnico | java | 

    > [!NOTE]
    > Neste guia de início rápido, criámos uma coleção não particionada. No entanto, se criar uma coleção particionada mediante a especificação de uma chave de partição durante a criação da coleção, terá de incluir a chave da partição como uma chave em cada vértice novo. 

5. Clique em **OK**. Poderá ter de expandir o ecrã para ver **OK**, na parte inferior.

6. Clique em **Vértice Novo** novamente e adicione outro utilizador. Introduza a etiqueta *pessoa* e , em seguida, introduza as chaves e os valores seguintes:

    key|valor|Notas
    ----|----|----
    ID|rakesh|O identificador exclusivo do vértice. Se não especificar, é gerado um id automaticamente.
    género|masculino| 
    escola|MIT| 

7. Clique em **OK**. 

8. Clique em **Aplicar Filtro** com o filtro `g.V()` predefinido. Todos os utilizadores aparecem agora na lista **Resultados**. À medida que adiciona mais dados, pode utilizar filtros para limitar os resultados. Por predefinição, o Data Explorer utiliza `g.V()` para obter todos os vértices de um gráfico, mas pode alterar para uma [consulta de gráfico](tutorial-query-graph.md) diferente, como `g.V().count()`, para devolver a contagem de todos os vértices do gráfico no formato JSON.

9. Agora, podemos ligar rakesh e ashley. Confirme que **ashley** está selecionada na lista **Resultados**, e clique no botão Editar junto a **Destinos**, no canto inferior direito. Poderá ter de alargar a janela para ver a área **Propriedades**.

   ![Alterar o destino de um vértice de um gráfico](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

10. Na caixa **Destino**, escreva *rakesh*, na caixa **Etiqueta da extremidade**, escreva *Conhece* e clique na caixa de verificação.

   ![Adicionar uma ligação entre ashley e rakesh no Data Explorer](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

11. Agora, selecione **rakesh** na lista de resultados e veja se ashley e rakesh estão ligados. 

   ![Dois vértices ligados no Data Explorer](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    Também pode utilizar o Data Explorer para criar procedimentos armazenados, UDFs e acionadores, para realizar lógica empresarial do lado do servidor, bem como débito de escala. O Data Explorer expõe todos os acessos a dados programáticos incorporados que estão disponíveis nas APIs, mas disponibiliza acesso fácil aos seus dados no portal do Azure.



## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos: 

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)


