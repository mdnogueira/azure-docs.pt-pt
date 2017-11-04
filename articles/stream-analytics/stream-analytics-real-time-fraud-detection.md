# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Começar a utilizar o Azure Stream Analytics: deteção de fraudes em tempo real

Este tutorial fornece uma ilustração de ponto a ponto sobre como utilizar o Azure Stream Analytics. Saiba como: 

* Traga a transmissão em fluxo de eventos para uma instância de Event Hubs do Azure. Neste tutorial, irá utilizar uma aplicação que fornecemos que simula uma sequência de registos de metadados telemóvel.

* Escreva consultas como o SQL Server Stream Analytics para transformar dados, Agregar informações ou à procura de padrões. Verá como utilizar uma consulta para examinar o fluxo de entrada e procure chamadas que poderão ser fraudulentas.

* Envie os resultados para um sink de saída (armazenamento) pode analisar informações adicionais. Neste caso, irá enviar os dados de suspeita chamada para o armazenamento de Blobs do Azure.

Neste tutorial, utilizamos o exemplo de deteção de fraudes em tempo real com base nos dados de chamada telefónica. Mas técnica que é ilustrar é também adequada para outros tipos de deteção de fraudes, tais como o roubo de fraudes ou a identidade do cartão de crédito. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: Deteção de fraudes de telecomunicações e SIM em tempo real

Uma empresa de telecomunicações tem um grande volume de dados de chamadas recebidas. A empresa pretende detetar chamadas fraudulentas em tempo real, para que podem notificar os clientes ou encerrar o serviço para um número específico. Um tipo de fraude SIM envolve várias chamadas da mesma identidade em torno do mesmo tempo, mas em localizações geograficamente diferentes. Para detetar este tipo de fraudes, a empresa tem de examinar os registos de telefone recebidos e procure padrões específicos, neste caso, para chamadas efetuadas em torno da simultâneo em diferentes países. Os registos de telefone que inseridas nesta categoria são escritos para o armazenamento para análise subsequente.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, irá simular dados de chamada telefónica através da utilização de uma aplicação de cliente que gera os metadados de chamada telefónica de exemplo. Alguns dos registos que produz a aplicação aspeto chamadas fraudulentas. 

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* A aplicação do gerador de eventos de chamada. Pode obter esta transferindo o [TelcoGenerator.zip ficheiro](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) do Microsoft Download Center. Deszipe o pacote para uma pasta no seu computador. Se pretender ver a origem de código e executar a aplicação num depurador, pode obter o código de origem da aplicação de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows poderão bloquear o ficheiro. zip transferido. Se não é possível descomprimi-lo, o ficheiro com o botão direito e selecione **propriedades**. Se vir a mensagem "este ficheiro provém de outro computador e poderá ser bloqueado para ajudar a proteger neste computador", selecione o **desbloqueio** opção e, em seguida, clique em **aplicar**.

Se pretender examine os resultados da tarefa de análise de transmissão em fluxo, terá também uma ferramenta para visualizar o conteúdo de um contentor do Blob Storage do Azure. Se utilizar o Visual Studio, pode utilizar [ferramentas do Azure para Visual Studio](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) ou [Visual Studio Cloud Explorer](https://docs.microsoft.com/en-us/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Em alternativa, pode instalar as ferramentas autónomas como [Explorador de armazenamento do Azure](http://storageexplorer.com/) ou [Explorador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Criar hubs de eventos de inserção de um evento do Azure

Para analisar um fluxo de dados, *inserção* -lo no Azure. Uma forma típica para a ingestão de dados consiste em utilizar [Event Hubs do Azure](../event-hubs/event-hubs-what-is-event-hubs.md), que lhe permite ingestão de milhões de eventos por segundo e, em seguida, processar e armazenar as informações de evento. Para este tutorial, pode criar um hub de eventos e, em seguida, ter a aplicação do gerador de eventos de chamada de enviar dados de chamadas para esse hub de eventos. Para obter mais informações sobre os event hubs, consulte o [documentação do Service Bus do Azure](https://docs.microsoft.com/en-us/azure/service-bus/).

>[!NOTE]
>Para uma versão mais detalhada deste procedimento, consulte [criar um espaço de nomes de Event Hubs e um hub de eventos no portal do Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Criar um hub de espaço de nomes e eventos
Neste procedimento, tem primeiro de criar um espaço de nome de hub de eventos e, em seguida, adicione um hub de eventos para esse espaço de nomes. Espaços de nomes de hub de eventos são utilizados para agrupar logicamente as instâncias de barramento de eventos relacionados. 

1. Inicie sessão no portal do Azure e clique em **novo** > **Internet das coisas** > **Hub de eventos**. 

2. No **criar espaço de nomes** painel, introduza um nome de espaço de nomes, como `<yourname>-eh-ns-demo`. Pode utilizar qualquer nome para o espaço de nomes, mas o nome tem de ser válido para um URL e tem de ser exclusivo em todo o Azure. 
    
3. Selecionar uma subscrição e crie ou escolha um grupo de recursos e clique em **criar**. 

    ![Criar um espaço de nomes do hub de eventos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png)
 
4. Quando o espaço de nomes tiver concluído a implementação, localize o espaço de nomes do hub de eventos na sua lista de recursos do Azure. 

5. Clique em novo espaço de nomes e, no painel de espaço de nomes, clique em **Hub de eventos**.

    ![Botão Adicionar Hub de eventos para criar um novo hub de eventos ](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Nome do novo hub de eventos `sa-eh-frauddetection-demo`. Pode utilizar um nome diferente. Se o fizer, tome nota do mesmo, porque tem o nome de mais tarde. Não é necessário definir quaisquer outras opções para o hub de eventos neste momento.

    ![Painel para criar um novo hub de eventos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png)
    
 
7. Clique em **Criar**.
### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceda acesso para o hub de eventos e obter uma cadeia de ligação

Antes de um processo pode enviar dados para um hub de eventos, o hub de eventos tem de ter uma política que permite o acesso adequado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  No painel de espaço de nomes do evento, clique em **Event Hubs** e, em seguida, clique no nome do seu hub de eventos novos.

2.  No painel do hub de eventos, clique em **políticas de acesso partilhado** e, em seguida, clique em  **+ &nbsp;adicionar**.

    >[!NOTE]
    >Certifique-se de que está a trabalhar com o hub de eventos, não o event hub espaço de nomes.

3.  Adicionar uma política com o nome `sa-policy-manage-demo` e para **afirmação**, selecione **gerir**.

    ![Painel para criar uma nova política de acesso do hub de eventos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png)
 
4.  Clique em **Criar**.

5.  Depois de implementar a política, clique na mesma na lista de políticas de acesso partilhado.

6.  Localizar a caixa de etiqueta **chave primária de cadeia de ligação** e clique no botão Copiar junto a cadeia de ligação. 
    
    ![Copiar a chave de cadeia de ligação principal da política de acesso](./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png)
 
7.  Cole a cadeia de ligação no editor de texto. Precisa desta cadeia de ligação para a secção seguinte, depois de efetuar algumas edições pequenas ao mesmo.

    A cadeia de ligação tem o seguinte aspeto:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=sa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=sa-eh-frauddetection-demo

    Tenha em atenção que a cadeia de ligação contém vários pares chave-valor, separadas por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar a aplicação do gerador de eventos

Antes de iniciar a aplicação de TelcoGenerator, configurá-lo de modo a que irá enviar registos de chamada para o hub de eventos que criou.

### <a name="configure-the-telcogeneratorapp"></a>Configurar o TelcoGeneratorapp

1.  No editor de onde copiou a cadeia de ligação, anote o `EntityPath` valor e, em seguida, remova o `EntityPath` par (não se esqueça de remover o ponto e vírgula que precede-lo). 

2.  Na pasta onde unzipped que o ficheiro TelcoGenerator.zip, abra o ficheiro de telcodatagen.exe.config num editor. (Há mais de um ficheiro. config, por isso, certifique-se que abrir o um direito.)

3.  No `<appSettings>` elemento:

    * Defina o valor da `EventHubName` chave para o nome de hub de eventos (ou seja, para o valor do caminho da entidade).
    * Defina o valor da `Microsoft.ServiceBus.ConnectionString` chave para a cadeia de ligação. 

    O `<appSettings>` secção terá um aspeto semelhante ao seguinte exemplo. (Para efeitos de clareza, iremos encapsulada as linhas e removido alguns carateres o token de autorização.)

    ![Ficheiro de configuração de aplicação TelcoGenerator que mostra a cadeia de ligação e nome de hub do evento](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4.  Guarde o ficheiro. 

### <a name="start-the-app"></a>Iniciar a aplicação
1.  Abra uma janela de comandos e altere para a pasta onde a aplicação de TelcoGenerator está deszipada.
2.  Introduza o seguinte comando:

        telcodatagen.exe 1000 .2 2

    Os parâmetros são: 

    * Número de CDRs por hora. 
    * Probabilidade de fraude de cartões SIM: Frequência, como uma percentagem de todas as chamadas, que a aplicação deve simular uma chamada fraudulenta. O valor.2 significa que cerca de 20% dos registos chamada terá um aspeto fraudulentas.
    * Duração em horas. O número de horas que deve executar a aplicação. Também pode parar a aplicação qualquer momento, premindo Ctrl + C na linha de comandos.

    Após alguns segundos, a aplicação for iniciada apresentar registos de chamada telefónica no ecrã de como envia-os para o hub de eventos.

Alguns dos campos chaves que irá utilizar nesta aplicação de deteção de fraudes em tempo real são os seguintes:

|**Registo**|**Definição**|
|----------|--------------|
|`CallrecTime`|O carimbo de hora de início de chamada. |
|`SwitchNum`|O comutador de telefone utilizado para ligar a chamada. Neste exemplo, os comutadores são cadeias que representam o país/região de origem (E.U.A., China, RU, Datacenters ou Austrália). |
|`CallingNum`|O número de telefone do chamador. |
|`CallingIMSI`|A identidade do subscritor móveis internacional (IMSI). Este é o identificador exclusivo do chamador. |
|`CalledNum`|O número de telefone do destinatário da chamada. |
|`CalledIMSI`|Identidade do subscritor móveis internacional (IMSI). Este é o identificador exclusivo do destinatário da chamada. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Criar uma tarefa de Stream Analytics para gerir os dados de transmissão em fluxo

Agora que tem um fluxo de eventos de chamada, pode configurar uma tarefa de Stream Analytics. A tarefa irá ler os dados do hub de eventos que configurou. 

### <a name="create-the-job"></a>Criar a tarefa 

1. No portal do Azure, clique em **novo** > **Internet das coisas** > **tarefa do Stream Analytics**.

2. A tarefa de nome `sa_frauddetection_job_demo`, especifique uma subscrição, o grupo de recursos e a localização.

    É uma boa ideia colocar a tarefa e o hub de eventos na mesma região para um melhor desempenho e a que não paga transferir dados entre regiões.

    ![Criar nova tarefa de Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png)

3. Clique em **Criar**.

    A tarefa é criada e o portal apresenta os detalhes da tarefa. Nada se encontra em execução, embora — tem de configurar a tarefa antes que possa ser iniciado.

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. No dashboard ou a **todos os recursos** painel, localize e selecione o `sa_frauddetection_job_demo` tarefa do Stream Analytics. 
2. No **tarefa topologia** secção do painel de tarefas do Stream Analytics, clique em de **entrada** caixa.

    ![Caixa de entrada em topologia no painel de tarefa de análise de transmissão em fluxo](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Clique em  **+ &nbsp;adicionar** e, em seguida, preencha o painel com estes valores:

    * **Alias de entrada**: Utilize o nome `CallStream`. Se utilizar um nome diferente, tome nota do mesmo porque precisará dela mais tarde.
    * **Tipo de origem**: selecione **fluxo de dados**. (**Referência a dados** refere-se aos dados de pesquisa estático, o que não irão utilizar neste tutorial.)
    * **Origem**: selecione **hub de eventos**.
    * **Opção de importar**: selecione **hub de eventos de utilização da subscrição atual**. 
    * **O espaço de nomes do Service bus**: selecione o espaço de nomes de hub de eventos que criou anteriormente (`<yourname>-eh-ns-demo`).
    * **Hub de eventos**: selecione o hub de eventos que criou anteriormente (`sa-eh-frauddetection-demo`).
    * **Nome de política do hub de eventos**: selecione a política de acesso que criou anteriormente (`sa-policy-manage-demo`).

    ![Criar nova entrada para a tarefa de análise de transmissão em fluxo](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png)

4. Clique em **Criar**.

## <a name="create-queries-to-transform-real-time-data"></a>Criar consultas para transformar dados em tempo real

Neste momento, tem uma tarefa de Stream Analytics configurar para ler um fluxo de dados de entrada. O passo seguinte consiste em criar uma transformação que analisa os dados em tempo real. Fazê-lo através da criação de uma consulta. Stream Analytics suporta um modelo de consulta simples e declarativo que descreve transformações de processamento em tempo real. As consultas de utilizam um idioma de como o SQL Server que tem algumas extensões específicas do stream analytics. 

Uma consulta simples apenas poderá ler todos os dados de entrada. No entanto, muitas vezes, criar consultas que ter um aspeto de dados específicos ou relações dos dados. Nesta secção do tutorial, pode criar e testar várias consultas para saber mais algumas formas em que pode transformar um fluxo de entrada para análise. 

As consultas que cria aqui apenas irão apresentar os dados transformados para o ecrã. Numa secção posterior, irá configurar um sink de saída e uma consulta que escreve os dados transformados para esse sink.

Para saber mais sobre o idioma, consulte o [referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Obter dados de exemplo para consultas de teste

A aplicação de TelcoGenerator está a enviar registos de chamada para o hub de eventos e a tarefa de Stream Analytics está configurada para ler a partir do hub de eventos. Pode utilizar uma consulta para testar a tarefa para se certificar de que está a ler corretamente. Para testar uma consulta na consola do Azure, terá de dados de exemplo. Nestas instruções, irá extrair dados de exemplo do fluxo que estará disponível para o hub de eventos.

1. Certifique-se de que a aplicação de TelcoGenerator está em execução e que produzem os registos de chamada.
2. No portal, regresse ao painel de tarefa de análise de transmissão em fluxo. (Se tiver fechado o painel, procure `sa_frauddetection_job_demo` no **todos os recursos** painel.)
3. Clique em de **consulta** caixa. Azure apresenta uma lista de entradas e saídas que estão configuradas para a tarefa e permite-lhe criar uma consulta que permite-lhe transformar o fluxo de entrada que é enviado para a saída.
4. No **consulta** painel, clique nos pontos junto a `CallStream` de entrada e, em seguida, selecione **dados de entrada de exemplo**.

    ![Opções de menu para utilizar dados de exemplo para a entrada da tarefa de análise de transmissão em fluxo, com "Dados de exemplo de entrada" selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)

    Esta ação abre um painel que lhe permite especificar quanto dados de exemplo para obter, definido em termos de quanto ao ler o fluxo de entrada.

5. Definir **minutos** para 3 e, em seguida, clique em **OK**. 
    
    ![Opções para o fluxo de entrada de amostragem com "3 minutos" selecionados.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure amostras de visão de 3 minutos de dados a partir do fluxo de entrada e notifica-o quando os dados de exemplo estão prontos. (Esta ação demora algum.) 

Os dados de exemplo são temporariamente armazenados e estão disponíveis enquanto tiver de abrir a janela de consulta. Se fechar a janela de consulta, os dados de exemplo são rejeitados e terá de criar um novo conjunto de dados de exemplo. 

Como alternativa, pode obter um ficheiro. JSON que tenha dados de exemplo na mesma [a partir do GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)e, em seguida, carregue esse ficheiro. JSON para utilizar como dados de exemplo para o `CallStream` entrada. 

### <a name="test-using-a-pass-through-query"></a>Testar uma consulta pass-through

Se pretende arquivar cada evento, pode utilizar uma consulta pass-through para ler todos os campos no payload do evento.

1. Na janela da consulta, introduza esta consulta:

        SELECT 
            *
        FROM 
            CallStream

    >[!NOTE]
    >Tal como acontece com o SQL Server, palavras-chave não são sensíveis a maiúsculas e espaços em branco não é significativo.

    Esta consulta, `CallStream` é o alias que especificou quando criou a entrada. Se utilizou um alias diferente, utilize esse nome em vez disso.

2. Clique em **teste**.

    A tarefa de Stream Analytics executada a consulta os dados de exemplo e apresenta a saída na parte inferior da janela. Isto indica que o hub de eventos e a tarefa de análise de transmissão em fluxo estão configurados corretamente. (Conforme indicado, mais tarde, irá criar um sink de saída que a consulta pode escrever dados.)

    ![Tarefa de Stream Analytics saída, que mostra 73 registos gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    O número exato de registos que vir dependerá registos quantos tiverem sido capturados num seu exemplo de 3 minutos.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Reduza o número de campos utilizando uma projeção de coluna

Em muitos casos, a análise não necessita de todas as colunas do fluxo de entrada. Pode utilizar uma consulta para projetar um conjunto de campos devolvidos na consulta pass-through mais pequeno.

1. Altere a consulta no editor de código para o seguinte:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
        FROM 
            CallStream

2. Clique em **teste** novamente. 

    ![Tarefa de Stream Analytics uma saída de projeção, que mostra 25 registos gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contagem de receber chamadas por região: janela em cascata com agregação

Suponha que pretende para contabilizar o número de chamadas recebidas por região. Dados de transmissão em fluxo, quando pretender executar as funções de agregação, como contar, terá de segmentar o fluxo em unidades temporais (uma vez que o fluxo de dados em si é efetivamente endless). Fazê-lo utilizando uma análise de transmissão em fluxo [função de janela](stream-analytics-window-functions.md). Em seguida, pode trabalhar com os dados dentro dessa janela como uma unidade.

Para esta transformação, pretende uma sequência de temporais windows que não se sobreponham — cada janela terá um conjunto de dados que pode agrupar e agregado discreto. Este tipo de janela é referido como um *janela em cascata* . Na janela em cascata, pode obter uma contagem de chamadas recebidas agrupados por `SwitchNum`, que representa o país/região onde a chamada foi originada. 

1. Altere a consulta no editor de código para o seguinte:

        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Esta consulta utiliza a `Timestamp By` palavra-chave no `FROM` cláusula para especificar o campo que timestamp no fluxo de entrada a utilizar para definir a janela em cascata. Neste caso, a janela divide os dados em segmentos pelo `CallRecTime` campo em cada registo. (Não se for especificado nenhum campo, a operação de modos de janela utiliza a hora em que cada evento chegar ao hub de eventos. Consulte "Tempo Vs aplicação hora da chegada de" em [referência de linguagem de consulta de análise de sequência](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    A projecção inclui `System.Timestamp`, que devolve um carimbo para o fim da janela de cada. 

    Para especificar que pretende utilizar uma janela em cascata, pode utilizar o [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) funcionar o `GROUP BY `cláusula. Na função, especifique uma unidade de tempo (em qualquer lugar a partir de aos microssegundos para um dia) e um tamanho de janela (unidades quantos). Neste exemplo, a janela em cascata é composta por intervalos de 5 segundos, pelo que irá obter uma contagem por país para visão de cada 5 segundos de chamadas.

2. Clique em **teste** novamente. Nos resultados, repare que a carimbos em **WindowEnd** são em incrementos de 5 segundos.

    ![Tarefa de Stream Analytics uma saída de agregação, que mostra 13 registos gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Detetar fraude SIM através de uma associação automática

Neste exemplo, vamos pode considerar a utilização fraudulenta ser chamadas provir do mesmo utilizador, mas em diferentes localizações dentro de 5 segundos um do outro. Por exemplo, o mesmo utilizador legitimamente não é possível efetuar uma chamada do E.U.A. e da Austrália ao mesmo tempo. 

Para verificar nestes casos, pode utilizar uma associação automática dos dados de transmissão em fluxo para associar o fluxo da com base no `CallRecTime` valor. Em seguida, pode procurar chamada regista onde o `CallingIMSI` valor (o número de origem) é o mesmo, mas o `SwitchNum` valor (país de origem) não é o mesmo.

Quando utiliza uma associação com dados de transmissão em fluxo, a associação tem de fornecer que alguns limites para as linhas correspondentes podem ser separados no tempo. (Conforme indicado anteriormente, os dados de transmissão em fluxo são efetivamente endless.) Os limites de tempo para a relação são especificados no interior do `ON` cláusula de associação, utilizando o `DATEDIFF` função. Neste caso, a associação baseia-se num intervalo de 5 segundos de dados de chamada.

1. Altere a consulta no editor de código para o seguinte: 

        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum

    Esta consulta é como qualquer associação do SQL Server, exceto para o `DATEDIFF` função na União. Esta é uma versão de `DATEDIFF` que é específica para análise de transmissão em fluxo e tem de aparecer no `ON...BETWEEN` cláusula. Os parâmetros são uma unidade de tempo (segundos neste exemplo) e os aliases de duas origens para a associação. (Isto é diferente do SQL Server standard `DATEDIFF` função.) 

    O `WHERE` cláusula inclui a condição que sinalizadores a chamada fraudulenta: os comutadores de origem não são iguais. 

2. Clique em **teste** novamente. 

    ![Tarefa de Stream Analytics uma saída de associação automática, que mostra 6 registos gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Clique em **Guardar**. Isto poupa a consulta de associação automática como parte da tarefa de análise de transmissão em fluxo. (Se não guardar os dados de exemplo.)

    ![Guardar a tarefa de Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png)

## <a name="create-an-output-sink-to-store-transformed-data"></a>Criar um sink de saída para armazenar os dados transformados

Definiu um fluxo de eventos, um hub de eventos de entrada para a ingestão de eventos e uma consulta ao executar uma transformação sobre o fluxo. O último passo consiste em definir um sink de saída para a tarefa — ou seja, um local para escrever o fluxo transformado para. 

Pode utilizar muitos recursos como saída sinks — uma base de dados do SQL Server, o table storage, armazenamento do Data Lake, Power BI e até mesmo outro hub de eventos. Para este tutorial, irá escrever o fluxo de Blob Storage do Azure, que é uma escolha típica para recolher informações de eventos para análise posterior, uma vez que o se adapta a dados não estruturados.

Se tiver uma conta de armazenamento de blob existente, pode utilizar isso. Para este tutorial, vamos mostrar-lhe como criar uma nova conta de armazenamento apenas para este tutorial.

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta do Blob Storage do Azure

1. No portal do Azure, regresse ao painel de tarefa de análise de transmissão em fluxo. (Se tiver fechado o painel, procure `sa_frauddetection_job_demo` no **todos os recursos** painel.)
2. No **tarefa topologia** secção, clique em de **saída** caixa. 
3. No **saídas** painel, clique em  **+ &nbsp;adicionar** e, em seguida, preencha o painel com estes valores:

    * **O alias de saída**: Utilize o nome `CallStream-FraudulentCalls`. 
    * **Sink**: selecione **armazenamento de BLOBs**.
    * **Importar opções**: selecione **armazenamento de BLOBs de utilização da subscrição atual**.
    * **Conta de armazenamento**. Selecione **criar nova conta de armazenamento.**
    * **Conta de armazenamento** (segunda caixa). Introduza `YOURNAMEsademo`, onde `YOURNAME` é o nome ou de outra cadeia exclusiva. O nome pode utilizar apenas letras minúsculas e números, e tem de ser exclusivo em todo o Azure. 
    * **Contentor**. Introduza `sa-fraudulentcalls-demo`.
    O nome da conta de armazenamento e o nome do contentor são utilizados em conjunto para fornecer um URI para o armazenamento de BLOBs, como esta: 

    `http://yournamesademo.blob.core.windows.net/sa-fraudulentcalls-demo/...`
    
    ![Painel "Novo de saída" para a tarefa de Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png)
    
4. Clique em **Criar**. 

    O Azure cria a conta de armazenamento e gera automaticamente uma chave. 

5. Fechar o **saídas** painel. 

## <a name="start-the-streaming-analytics-job"></a>Iniciar a tarefa de análise de transmissão em fluxo

A tarefa está agora configurada. Já especificou uma entrada (o hub de eventos), uma transformação (a consulta para procurar chamadas fraudulentas) e um resultado (armazenamento de BLOBs). É agora possível iniciar a tarefa. 

1. Certifique-se que a aplicação de TelcoGenerator está em execução.

2. No painel de tarefas, clique em **iniciar**.

    ![Iniciar a tarefa de Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-output.png)

3. No **iniciar tarefa** painel, para a tarefa saída hora de início, selecione **agora**. 

4. Clique em **iniciar**. 

    ![Painel "Iniciar a tarefa" para a tarefa de Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-job-blade.png)

    Azure notifica-o quando a tarefa foi iniciada e, no painel de tarefas, o estado é apresentado como **executar**.

    ![Tarefa de Stream Analytics Estado, mostrar "Em execução"](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-running-status.png)
    

## <a name="examine-the-transformed-data"></a>Examine os dados transformados

Tem agora uma tarefa de transmissão em fluxo análise completa. A tarefa é examinar um fluxo de metadados de chamada telefónica, à procura de chamadas telefónicas fraudulentas em tempo real e escrever informações sobre essas fraudulentas chamadas para o armazenamento. 

Para concluir este tutorial, pode querer ver os dados que está a ser capturados pela tarefa de análise de transmissão em fluxo. Os dados está a ser escritos para o armazenamento de blogue do Azure em segmentos (ficheiros). Pode utilizar qualquer ferramenta que lê o Blob Storage do Azure. Conforme indicado na secção pré-requisitos, pode utilizar extensões do Azure no Visual Studio, ou pode utilizar uma ferramenta como o [Explorador de armazenamento do Azure](http://storageexplorer.com/) ou [Explorador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction). 

Ao examinar o conteúdo de um ficheiro no armazenamento de BLOBs, verá algo como o seguinte:

![Armazenamento de Blobs do Azure com a análise de transmissão em fluxo de saída](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Limpar recursos

Temos de artigos adicionais que continuar com o cenário de deteção de fraudes e que utilizam os recursos que criou neste tutorial. Se pretender continuar, consulte as sugestões em **passos** mais tarde.

No entanto, se tiver terminado e não precisa de recursos que criou, pode eliminá-los para que poderá não pagar desnecessários do Azure. Nesse caso, sugerimos que pode fazer o seguinte:

1. Pare a tarefa de análise de transmissão em fluxo. No **tarefas** painel, clique em **parar** na parte superior.
2. Parar o Telco gerador de aplicação. Na janela de comando onde começou a aplicação, prima Ctrl + C.
3. Se tiver criado uma nova conta de armazenamento de blob apenas para este tutorial, elimine-o. 
4. Elimine a tarefa de análise de transmissão em fluxo.
5. Elimine o hub de eventos.
6. Elimine o espaço de nomes do hub de eventos.

## <a name="get-support"></a>Obter suporte

Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

Pode continuar a este tutorial com o seguinte artigo:

* [Stream Analytics e o Power BI: um dashboard de análise em tempo real para dados de transmissão em fluxo](stream-analytics-power-bi-dashboard.md). Este artigo mostra como enviar o resultado de TelCo da tarefa de Stream Analytics para o Power BI para visualização em tempo real e análise.

Para obter mais informações sobre o Stream Analytics em geral, consulte estes artigos:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
