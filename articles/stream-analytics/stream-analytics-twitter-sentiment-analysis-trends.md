---
title: "Análise de dados de sentimento do Twitter em tempo real com o Azure Stream Analytics | Microsoft Docs"
description: "Saiba como utilizar o Stream Analytics para análise de dados de sentimento do Twitter em tempo real. Orientações passo a passo da geração de eventos para dados num dashboard em direto."
keywords: "análise de tendências do twitter em tempo real, análise de dados de sentimento, análise de redes sociais, exemplo de análise de tendências"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: samacha
ms.openlocfilehash: 98230a8b61d1776a9ab23fd416af306efc700959
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de dados de sentimento do Twitter em tempo real no Azure Stream Analytics

Saiba como criar uma solução de análise de dados de sentimento para análise de redes sociais ao colocar em tempo real Twitter eventos em Event Hubs do Azure. Pode, em seguida, utilize uma consulta do Azure Stream Analytics para analisar os dados e a armazenar os resultados para posterior de escrita ou um dashboard e [Power BI](https://powerbi.com/) para fornecer informações em tempo real.

As ferramentas de análise de redes sociais ajudam as organizações a compreender as tendências de tópicos. Tópicos de tendências são assuntos e attitudes que tem um elevado volume de mensagens de redes sociais. Análise de dados de sentimento, também denominada *extração opinião*, utiliza as ferramentas de análise de redes sociais para determinar attitudes na direção de um produto, ideia e assim sucessivamente. 

Análise de tendências em tempo real do Twitter é um excelente exemplo de uma ferramenta de análise, porque o modelo de subscrição de hashtag permite-lhe escutar específicas palavras-chave (hashtags) e desenvolva a análise de dados de sentimento do feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de dados de sentimento de redes sociais em tempo real

Uma empresa que tenha um site de suporte de dados de notícias está interessada em obter vantagens sobre a respetivas concorrentes por featuring conteúdo do site imediatamente relevantes para os leitores. A empresa utiliza a análise de redes sociais tópicos que são relevantes para os leitores efetuando análise em tempo real de dados de sentimento do Twitter dados.

Para identificar tendências de tópicos em tempo real no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweet e dados de sentimento para os principais tópicos. Por outras palavras, a necessidade é um motor de análise de análise de sentimento com base neste feed de suporte de dados sociais.

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial, utilize uma aplicação cliente que liga-se ao Twitter e procura tweets com determinadas hashtags (que pode definir). Para executar a aplicação e analisar os tweets através da análise de transmissão em fluxo do Azure, tem de ter o seguinte:

* Uma subscrição do Azure.
* Uma conta do Twitter 
* Uma aplicação do Twitter e o [token de acesso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) para essa aplicação. Fornecemos instruções de alto nível sobre como criar uma aplicação do Twitter mais tarde.
* A aplicação TwitterWPFClient, que lê o feed do Twitter. Para obter esta aplicação, transfira o [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) de ficheiros a partir do GitHub e, em seguida, deszipe o pacote para uma pasta no seu computador. Se pretender ver a origem de código e executar a aplicação num depurador, pode obter o código de origem do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Criar um hub de eventos para análise de transmissão em fluxo de entrada

A aplicação de exemplo gera eventos e envia-os para um hub de eventos do Azure. Event hubs do Azure são o método preferencial de ingestão de eventos para o Stream Analytics. Para obter mais informações, consulte o [documentação de Event Hubs do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Criar um espaço de nomes de hub de eventos e o hub de eventos
Neste procedimento, tem primeiro de criar um espaço de nome de hub de eventos e, em seguida, adicione um hub de eventos para esse espaço de nomes. Espaços de nomes de hub de eventos são utilizados para agrupar logicamente as instâncias de barramento de eventos relacionados. 

1. Inicie sessão no portal do Azure e clique em **novo** > **Internet das coisas** > **Hub de eventos**. 

2. No **criar espaço de nomes** painel, introduza um nome de espaço de nomes, como `<yourname>-socialtwitter-eh-ns`. Pode utilizar qualquer nome para o espaço de nomes, mas o nome tem de ser válido para um URL e tem de ser exclusivo em todo o Azure. 
    
3. Selecionar uma subscrição e crie ou escolha um grupo de recursos e clique em **criar**. 

    ![Criar um espaço de nomes do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Quando o espaço de nomes tiver concluído a implementação, localize o espaço de nomes do hub de eventos na sua lista de recursos do Azure. 

5. Clique em novo espaço de nomes e, no painel do espaço de nomes, clique em  **+ &nbsp;Hub de eventos**. 

    ![Botão Adicionar Hub de eventos para criar um novo hub de eventos ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nome do novo hub de eventos `socialtwitter-eh`. Pode utilizar um nome diferente. Se o fizer, tome nota do mesmo, porque tem o nome de mais tarde. Não é necessário definir quaisquer outras opções para o hub de eventos.

    ![Painel para criar um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Clique em **Criar**.


### <a name="grant-access-to-the-event-hub"></a>Conceder acesso para o hub de eventos

Antes de um processo pode enviar dados para um hub de eventos, o hub de eventos tem de ter uma política que permite o acesso adequado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  No painel de espaço de nomes do evento, clique em **Event Hubs** e, em seguida, clique no nome do seu hub de eventos novos.

2.  No painel do hub de eventos, clique em **políticas de acesso partilhado** e, em seguida, clique em  **+ &nbsp;adicionar**.

    >[!NOTE]
    >Certifique-se de que está a trabalhar com o hub de eventos, não o event hub espaço de nomes.

3.  Adicionar uma política com o nome `socialtwitter-access` e para **afirmação**, selecione **gerir**.

    ![Painel para criar uma nova política de acesso do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Clique em **Criar**.

5.  Depois de implementar a política, clique na mesma na lista de políticas de acesso partilhado.

6.  Localizar a caixa de etiqueta **chave primária de cadeia de ligação** e clique no botão Copiar junto a cadeia de ligação. 
    
    ![Copiar a chave de cadeia de ligação principal da política de acesso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Cole a cadeia de ligação no editor de texto. Precisa desta cadeia de ligação para a secção seguinte, depois de efetuar algumas edições pequenas ao mesmo.

    A cadeia de ligação tem o seguinte aspeto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Tenha em atenção que a cadeia de ligação contém vários pares chave-valor, separadas por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

    > [!NOTE]
    > Para segurança, foram removidas partes da cadeia de ligação no exemplo.

8.  No editor de texto, remova o `EntityPath` par da cadeia de ligação (não se esqueça de remover o ponto e vírgula que precede-lo). Quando tiver terminado, a cadeia de ligação tem este aspeto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar a aplicação de cliente do Twitter
A aplicação cliente obtém os eventos de tweet diretamente a partir do Twitter. Para tal, tem de permissão para chamar as APIs de transmissão em fluxo Twitter. Para configurar essa permissão, criar uma aplicação no Twitter, o que gera exclusivas credenciais (por exemplo, um token de OAuth). Em seguida, pode configurar a aplicação de cliente para utilizar estas credenciais quando faz chamadas da API. 

### <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter
Se ainda não tiver uma aplicação do Twitter que pode utilizar para este tutorial, pode criar uma. Já tem de ter uma conta do Twitter.

> [!NOTE]
> Poderá alterar o processo de exato no Twitter para criar uma aplicação e obter as chaves, os segredos e token. Se estas instruções não corresponderem, o que veem no site do Twitter, consulte a documentação de programador do Twitter.

1. Vá para o [página de gestão de aplicações do Twitter](https://apps.twitter.com/). 

2. Crie uma nova aplicação. 

    * Para o URL de Web site, especifique um URL válido. Não tem como um site em direto. (Não é possível especificar apenas `localhost`.)
    * O campo de chamada de retorno deixe em branco. A aplicação de cliente a que utilizar para este tutorial não requer chamadas de retorno.

    ![Criar uma aplicação no Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Opcionalmente, altere as permissões da aplicação para só de leitura.

4. Quando a aplicação é criada, vá para o **chaves e os Tokens de acesso** página.

5. Clique no botão para gerar um segredo do token acesso e de token de acesso.

Manter estas informações à mão, pois precisará no procedimento seguinte.

>[!NOTE]
>As chaves e segredos para a aplicação do Twitter fornecem acesso à sua conta do Twitter. Trate estas informações como confidencial, da mesma forma que a palavra-passe do Twitter. Por exemplo, não incorpore estas informações numa aplicação que está a conceder a outras pessoas. 


### <a name="configure-the-client-application"></a>Configurar a aplicação de cliente
Criámos uma aplicação de cliente que liga à utilização de dados do Twitter [APIs de transmissão em fluxo do Twitter](https://dev.twitter.com/streaming/overview) para recolher eventos de tweet sobre um conjunto específico de tópicos. A aplicação utiliza o [Sentiment140](http://help.sentiment140.com/) ferramenta de código aberto, o que atribui o seguinte valor de dados de sentimento para cada tweet:

* 0 = negativo
* 2 = independente
* 4 = positivo

Depois dos eventos de tweet foi atribuídos um valor de dados de sentimento, são enviadas por push para o hub de eventos que criou anteriormente.

Antes da aplicação é executada, necessita de determinadas informações do utilizador, como as chaves do Twitter e a cadeia de ligação do hub de eventos. Pode fornecer as informações de configuração das seguintes maneiras:

* Executar a aplicação e, em seguida, utilize a IU da aplicação para introduzir as chaves, os segredos e cadeia de ligação. Se o fizer, as informações de configuração são utilizadas para a sua sessão atual, mas não é guardada.
* Editar o ficheiro. config da aplicação e definir os valores não existe. Esta abordagem persiste as informações de configuração, mas também significa que estas informações potencialmente confidenciais são armazenadas em texto simples no seu computador.

O procedimento seguinte documentos ambas as abordagens. 

1. Certifique-se já transferiu e deszipada o [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) aplicação, tal como indicado nos pré-requisitos.

2. Para definir a execução de valores em tempo de execução (e apenas para a sessão atual), o `TwitterWPFClient.exe` aplicação. Quando a aplicação pede-lhe, introduza os valores seguintes:

    * A chave de consumidor do Twitter (chave de API).
    * O segredo de consumidor do Twitter (API segredo).
    * O Token de acesso do Twitter.
    * O Twitter acesso Token segredo.
    * As informações de cadeia de ligação que guardou anteriormente. Certifique-se de que utiliza a cadeia de ligação que removeu o `EntityPath` pares chave-valor do.
    * As palavras-chave de Twitter que pretende determinar sentimento para.

   ![TwitterWpfClient aplicação em execução, que mostra as definições de ficar obscurecidas](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Para definir os valores de forma permanente, utilize um editor de texto para abrir o ficheiro TwitterWpfClient.exe.config. Em seguida, no `<appSettings>` elemento, fazê-lo:

    * Definir `oauth_consumer_key` para a chave de consumidor do Twitter (chave de API). 
    * Definir `oauth_consumer_secret` para o segredo do consumidor do Twitter (API segredo).
    * Definir `oauth_token` o token de acesso do Twitter.
    * Definir `oauth_token_secret` para o segredo de Token de acesso de Twitter.

    Mais à frente do `<appSettings>` elemento, efetuar estas alterações:

    * Definir `EventHubName` para o nome de hub de eventos (ou seja, para o valor do caminho da entidade).
    * Definir `EventHubNameConnectionString` à cadeia de ligação. Certifique-se de que utiliza a cadeia de ligação que removeu o `EntityPath` pares chave-valor do.

    O `<appSettings>` secção aspeto semelhante ao seguinte exemplo. (Para efeitos de clareza e segurança, iremos encapsulada algumas linhas e remover alguns carateres.)

    ![Ficheiro de configuração de aplicação TwitterWpfClient num editor de texto, que mostra as Twitter chaves e segredos e as informações de cadeia de ligação de hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Se já não foi iniciada a aplicação, execute agora TwitterWpfClient.exe. 

5. Clique no botão de início verde para recolher dados de sentimento redes sociais. Ver eventos de Tweet com o **CreatedAt**, **tópico**, e **SentimentScore** valores a ser enviados para o hub de eventos.

    ![TwitterWpfClient aplicação em execução, que mostra uma lista de tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Se observar erros e não vir um fluxo de tweets apresentada na parte inferior da janela, verifique as chaves e segredos. Além disso, verifique a cadeia de ligação (Certifique-se de que não inclua o `EntityPath` valor e chave.)


## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

Agora que eventos tweet são transmissão em fluxo em tempo real do Twitter, pode configurar uma tarefa de Stream Analytics para analisar estes eventos em tempo real.

1. No portal do Azure, clique em **novo** > **Internet das coisas** > **tarefa do Stream Analytics**.

2. A tarefa de nome `socialtwitter-sa-job` e especifique uma subscrição, o grupo de recursos e localização.

    É uma boa ideia colocar a tarefa e o hub de eventos na mesma região para um melhor desempenho e a que não paga transferir dados entre regiões.

    ![Criar uma nova tarefa de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Clique em **Criar**.

    A tarefa é criada e o portal apresenta os detalhes da tarefa.


## <a name="specify-the-job-input"></a>Especifique a entrada da tarefa

1. Na sua tarefa do Stream Analytics, sob **tarefa topologia** meio o painel de tarefas, clique em **entradas**. 

2. No **entradas** painel, clique em  **+ &nbsp;adicionar** e, em seguida, preencha o painel com estes valores:

    * **Alias de entrada**: Utilize o nome `TwitterStream`. Se utilizar um nome diferente, tome nota do mesmo porque precisar dele mais tarde.
    * **Tipo de origem**: selecione **fluxo de dados**.
    * **Origem**: selecione **hub de eventos**.
    * **Opção de importar**: selecione **hub de eventos de utilização da subscrição atual**. 
    * **O espaço de nomes do Service bus**: selecione o espaço de nomes de hub de eventos que criou anteriormente (`<yourname>-socialtwitter-eh-ns`).
    * **Hub de eventos**: selecione o hub de eventos que criou anteriormente (`socialtwitter-eh`).
    * **Nome de política do hub de eventos**: selecione a política de acesso que criou anteriormente (`socialtwitter-access`).

    ![Criar nova entrada para a tarefa de análise de transmissão em fluxo](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Clique em **Criar**.


## <a name="specify-the-job-query"></a>Especifique a consulta da tarefa

Stream Analytics suporta um modelo de consulta simples e declarativo que descreve transformações. Para saber mais sobre o idioma, consulte o [referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial ajuda-o a criar e testar várias consultas através de dados do Twitter.

Para comparar o número de menciona suportadas entre tópicos, pode utilizar um [janela em cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de menciona suportadas por tópico a cada cinco segundos.

1. Fechar o **entradas** painel se ainda não o fez.

2. No painel de tarefas, clique o **consulta** caixa. Azure apresenta uma lista de entradas e saídas que estão configuradas para a tarefa e permite-lhe criar uma consulta que permite-lhe transformar o fluxo de entrada que é enviado para a saída.

3. Certifique-se de que a aplicação de TwitterWpfClient está em execução. 

3. No **consulta** painel, clique nos pontos junto a `TwitterStream` de entrada e, em seguida, selecione **dados de entrada de exemplo**.

    ![Opções de menu para utilizar dados de exemplo para a entrada da tarefa de análise de transmissão em fluxo, com "Dados de exemplo de entrada" selecionados](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Esta ação abre um painel que lhe permite especificar quanto dados de exemplo para obter, definido em termos de quanto ao ler o fluxo de entrada.

4. Definir **minutos** para 3 e, em seguida, clique em **OK**. 
    
    ![Opções para o fluxo de entrada de amostragem com "3 minutos" selecionados.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure amostras de visão de 3 minutos de dados a partir do fluxo de entrada e notifica-o quando os dados de exemplo estão prontos. (Esta ação demora algum.) 

    Os dados de exemplo são temporariamente armazenados e estão disponíveis enquanto tiver de abrir a janela de consulta. Se fechar a janela de consulta, os dados de exemplo são rejeitados e tem de criar um novo conjunto de dados de exemplo. 

5. Altere a consulta no editor de código para o seguinte:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Se não utilizar `TwitterStream` como alias para a entrada, substitua o seu alias para `TwitterStream` na consulta.  

    Esta consulta utiliza a **TIMESTAMP BY** palavra-chave para especificar um campo timestamp no payload a ser utilizado na computação temporal. Se este campo não está especificado, a operação de modos de janela é efetuada utilizando a hora que cada evento chegou o hub de eventos. Obter mais informações na secção "Hora da chegada vs hora da aplicação" [referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Esta consulta também tem acesso a um carimbo para o fim da janela de cada utilizando o **Timestamp** propriedade.

5. Clique em **teste**. A consulta é executada relativamente os dados a amostragem.
    
6. Clique em **Guardar**. Isto poupa a consulta como parte da tarefa de análise de transmissão em fluxo. (Se não guardar os dados de exemplo.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentação através de diferentes campos da sequência 

A tabela seguinte lista os campos que fazem parte de transmissão em fluxo de dados JSON. Pode experimentar no editor de consultas.

|Propriedade JSON | Definição|
|--- | ---|
|createdAt | A hora em que foi criado o tweet|
|Tópico | O tópico que corresponda a palavra-chave especificada|
|SentimentScore | A classificação de dados de sentimento do Sentiment140|
|Autor | O identificador do Twitter que enviou o tweet|
|Texto | O corpo completo do tweet|


## <a name="create-an-output-sink"></a>Criar um sink de saída

Agora que definiu uma transmissão de eventos num hub de eventos de entrada para a ingestão de eventos e uma consulta ao executar uma transformação sobre o fluxo. O último passo consiste em definir um sink de saída para a tarefa.  

Neste tutorial, escrever os eventos de agregados tweet da consulta à tarefa para o armazenamento de Blobs do Azure.  Também pode enviar os resultados para SQL Database do Azure, Table storage do Azure, Event Hubs, ou o Power BI, dependendo da sua aplicação necessita.

## <a name="specify-the-job-output"></a>Especifique o resultado da tarefa

1. No **tarefa topologia** secção, clique em de **saída** caixa. 

2. No **saídas** painel, clique em  **+ &nbsp;adicionar** e, em seguida, preencha o painel com estes valores:

    * **O alias de saída**: Utilize o nome `TwitterStream-Output`. 
    * **Sink**: selecione **armazenamento de BLOBs**.
    * **Importar opções**: selecione **armazenamento de BLOBs de utilização da subscrição atual**.
    * **Conta de armazenamento**. Selecione **criar uma nova conta de armazenamento.**
    * **Conta de armazenamento** (segunda caixa). Introduza `YOURNAMEsa`, onde `YOURNAME` é o nome ou de outra cadeia exclusiva. O nome pode utilizar apenas letras minúsculas e números, e tem de ser exclusivo em todo o Azure. 
    * **Contentor**. Introduza `socialtwitter`.
    O nome da conta de armazenamento e o nome do contentor são utilizados em conjunto para fornecer um URI para o armazenamento de BLOBs, como esta: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![Painel "Novo de saída" para a tarefa de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Clique em **Criar**. 

    O Azure cria a conta de armazenamento e gera automaticamente uma chave. 

5. Fechar o **saídas** painel. 


## <a name="start-the-job"></a>Iniciar a tarefa

Uma entrada da tarefa, consulta e saída estão especificados. Está pronto para iniciar a tarefa de Stream Analytics.

1. Certifique-se de que a aplicação de TwitterWpfClient está em execução. 

2. No painel de tarefas, clique em **iniciar**.

    ![Iniciar a tarefa de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. No **iniciar tarefa** painel, para **hora de início do resultado da tarefa**, selecione **agora** e, em seguida, clique em **iniciar**. 

    ![Painel "Iniciar a tarefa" para a tarefa de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure notifica-o quando a tarefa foi iniciada e, no painel da tarefa, o estado é apresentado como **executar**.

    ![Tarefa em execução](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Vista de saída para análise de dados de sentimento

Depois da tarefa foi iniciada em execução e está a processar o fluxo em tempo real do Twitter, pode ver o resultado para análise de dados de sentimento.

Pode utilizar uma ferramenta como o [Explorador de armazenamento do Azure](https://http://storageexplorer.com/) ou [Explorador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver o resultado da tarefa em tempo real. Aqui, pode utilizar [Power BI](https://powerbi.com/) para expandir a sua aplicação para incluir um dashboard personalizado, como o mostrado na captura de ecrã seguinte:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Criar outra consulta para identificar tendências de tópicos

Outra consulta pode utilizar para compreender os dados de sentimento do Twitter baseia-se num [a deslizante janela](https://msdn.microsoft.com/library/azure/dn835051.aspx). Para identificar tendências de tópicos, procure tópicos sobre o que cruzam um valor de limiar para menciona suportadas dentro de um período de tempo especificado.

Para efeitos deste tutorial, procurar tópicos que são mencionados mais de 20 vezes nos últimos 5 segundos.

1. No painel de tarefas, clique em **parar** para parar a tarefa. 

2. No **tarefa topologia** secção, clique em de **consulta** caixa. 

3. Altere a consulta para o seguinte:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Clique em **Guardar**.

5. Certifique-se de que a aplicação de TwitterWpfClient está em execução. 

6. Clique em **iniciar** para reiniciar a tarefa utilizando a nova consulta.


## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
