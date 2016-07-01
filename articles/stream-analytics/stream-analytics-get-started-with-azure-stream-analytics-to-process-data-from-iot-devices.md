<properties
    pageTitle="Introdução ao Azure Stream Analytics para o processamento de dados a partir de dispositivos da IoT. | Stream Analytics"
    description="Etiquetas do sensor da IoT e transmissão de dados com análises de transmissão e processamento de dados em tempo real"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="paulettm"
    editor="cgronlun"
/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="05/03/2016"
    ms.author="jeffstok"
/>

# Introdução ao Azure Stream Analytics para o processamento de dados a partir de dispositivos da IoT

Neste tutorial, irá aprender a criar uma lógica de processamento da transmissão para reunir dados a partir de dispositivos da Internet das Coisas (IoT). Vamos analisar um caso real de utilização da Internet das Coisas (IoT) para mostrar como deve construir a solução rapidamente e de forma económica.

## Pré-requisitos

-   [Subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/)
-   A consulta de exemplos e os ficheiros de dados estão disponíveis para transferência em [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted)

## Cenário

Contoso é uma empresa de fabrico no espaço de automatização industrial que automatizou completamente o processo de fabrico. Nesta fábrica, a maquinaria tem sensores que emitem transmissões de dados em tempo real. Neste cenário, um gestor do piso de produção pretende ter conhecimentos aprofundados e em tempo real sobre os sensores de dados e tomar medidas relativamente aos mesmos. Utilizamos o Linguagem de Consulta do Stream Analytics (SAQL) para os dados de sensor para descobrir padrões interessantes sobre a transmissão de entrada de dados.

Aqui os dados estão a ser gerados a partir de um dispositivo Sensor Tag da Texas Instruments.

![Sensor Tag da Texas Instruments](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

O Payload dos dados está no formato JSON e tem o seguinte aspeto:

    
    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  
    
Num cenário do mundo real, terá 100 destes sensores a gerar eventos como uma transmissão. O ideal seria ter um dispositivo de gateway a executar alguns códigos para emitir estes eventos nos [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/). A tarefa do Stream Analytics seria consumir estes eventos a partir de Event Hubs e executar a análise em tempo real, expressa em consultas, e enviar os resultados para as saídas pretendidas.

Neste manual de introdução, fornecemos-lhe um ficheiro de dados de exemplo, gerado a partir de dispositivos SensorTag reais, no qual pode executar diferentes consultas e ver os respetivos resultados. Nos tutoriais subsequentes, ficará a saber como ligar a tarefa às entradas e saídas e como as implementar no serviço do Azure.

## Criar tarefa do Stream Analytics

No [Portal do Azure](http://manage.windowsazure.com), abra o Stream Analytics e clique em **"Novo"** no canto inferior esquerdo da página para criar uma nova tarefa de análise.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

Clique em "**criação rápida**".

Para a definição **"Monitorização regional da conta do Storage"**, selecione **"Criar nova conta do Storage"** e atribua-lhe um nome exclusivo. O Azure Stream Analytics utiliza esta conta para armazenar informações de monitorização de todas as tarefas futuras.

> [AZURE.NOTE] Deve criar esta conta do Storage apenas uma vez por região e ela será partilhada em todas as tarefas do Stream Analytics que foram criadas nessa região.

Clique em "**Criar tarefa do Stream Analytics**" na parte inferior da página.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Consulta do Azure Stream Analytics

Clique no separador Consulta para aceder ao editor de consultas. O separador Consulta contém uma consulta SQL que efetua a transformação através dos dados recebidos.

## Arquivo de dados não processados

A forma mais simples de consulta é um pass-through que arquiva todos os dados de entrada para o resultado designado.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Transfira agora o ficheiro de dados de exemplo a partir do [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted) para uma localização no computador. Copie e cole a consulta a partir do ficheiro **PassThrough.txt**. Clique no botão de teste em baixo e selecione o ficheiro de dados com o nome **HelloWorldASA InputStream.json** na localização de transferência.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

Pode ver os resultados da consulta no browser em baixo.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## Limpeza de dados com base numa condição

Vamos tentar filtrar os resultados com base numa condição. Gostaríamos de mostrar apenas os resultados provenientes do "SensorA". A consulta está localizada no ficheiro **Filtering.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Repare que estamos a comparar um valor de cadeia e as respetivas maiúsculas e minúsculas. Clique em **Voltar a executar** para executar a consulta. A consulta deverá devolver apenas 389 linhas de 1860 eventos.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## Alertas para acionar o fluxo de trabalho do negócio

Vamos então tornar a nossa consulta um pouco mais interessante. Para cada tipo de sensor, se quisermos monitorizar a temperatura média por cada janela de 30 segundos e apresentar os resultados apenas se a temperatura média estiver acima 100 graus, iremos escrever os dados da consulta em baixo e, em seguida, clicar em Voltar a executar para ver os resultados. A consulta encontra-se localizada no ficheiro **ThresholdAlerting.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Agora os resultados contêm apenas 245 linhas com esses sensores cuja temperatura média é superior a 100. Nesta consulta agrupámos a transmissão de eventos por dspl, que é o nome do sensor, e colocámo-la numa **janela em cascata** de 30 segundos. Quando realizamos estas consultas temporais, é essencial indicar como pretendemos que o tempo avance. Com a cláusula **TIMESTAMP BY**, especificámos a coluna "tempo" como uma forma de progresso do tempo para todos os cálculos temporais. Para obter informações detalhadas, leia o nosso tópicos MSDN sobre [Gestão do tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [Modos de janela](https://msdn.microsoft.com/library/azure/dn835019.aspx).

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## Localizar a falta de padrões

Como posso efetuar uma consulta para procurar a falta de padrões? Por exemplo, vamos descobrir quando foi a última vez que um sensor enviou dados e depois não enviou quaisquer eventos durante o minuto seguinte. A consulta encontra-se localizada no ficheiro **AbsenseOfEvent.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Aqui vamos utilizar uma **ASSOCIAÇÃO EXTERNA** para a mesma transmissão de dados (associação automática). Para uma associação interna, só é apresentado um resultado quando é encontrada uma correspondência.  Mas numa associação **EXTERNA**, se um evento externo à associação não tiver qualquer correspondência, é apresentada a linha NULO para todas as colunas da linha à direita. Esta técnica é muito útil para localizar a ausência de eventos. Para obter mais detalhes sobre como [PARTICIPAR](https://msdn.microsoft.com/library/azure/dn835026.aspx), consulte a nossa documentação MSDN.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## Conclusão

Basicamente, com este tutorial aprende a efetuar diferentes consultas SAQL e a ler os resultados no browser com os diferentes padrões de dados. No entanto, isto é apenas o início. O Stream Analytics tem muitas outras funcionalidades. Em seguida, irá aprender a conectar a tarefa do Stream Analytics às entradas e saídas e a implementá-la no Azure. Pode explorar ainda mais a utilização do Stream Analytics através do nosso manual com [mapa de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/) para obter mais informações sobre como efetuar consultas. Leia o artigo sobre [Padrões de consulta comuns](./stream-analytics-stream-analytics-query-patterns.md#query-example-detect-the-absence-of-events).



<!--HONumber=Jun16_HO2-->


