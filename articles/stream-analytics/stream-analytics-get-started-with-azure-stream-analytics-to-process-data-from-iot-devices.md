---
title: Introdução ao Azure Stream Analytics para o processamento de dados a partir de dispositivos da IoT. | Microsoft Docs
description: Etiquetas do sensor da IoT e transmissão de dados com análises de transmissão e processamento de dados em tempo real
keywords: solução iot, começar com iot
services: stream-analytics
documentationcenter: ''
author: jeffstokes72
manager: paulettm
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/11/2016
ms.author: jeffstok

---
# Introdução ao Azure Stream Analytics para o processamento de dados a partir de dispositivos da IoT
Neste tutorial, irá aprender a criar uma lógica de processamento da transmissão para reunir dados a partir de dispositivos da Internet das Coisas (IoT). Vamos analisar um caso real de utilização da Internet das Coisas (IoT) para mostrar como deve construir a solução rapidamente e de forma económica.

## Pré-requisitos
* [Subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/)
* A consulta de exemplos e os ficheiros de dados estão disponíveis para transferência em [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## Cenário
Contoso é uma empresa de automatização industrial que automatizou por completo o processo de fabrico. Nesta fábrica, as máquinas têm sensores que emitem transmissões de dados em tempo real. Neste cenário, um gestor do piso de produção pretende ter conhecimentos aprofundados e em tempo real sobre os sensores de dados e tomar medidas relativamente aos mesmos. Utilizamos o Linguagem de Consulta do Stream Analytics (SAQL) para os dados de sensor para descobrir padrões interessantes sobre a transmissão de entrada de dados.

Aqui os dados estão a ser gerados a partir de um dispositivo Sensor Tag da Texas Instruments.

![Sensor Tag da Texas Instruments](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

O Payload dos dados está no formato JSON e tem o seguinte aspeto:

    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

Num cenário real, pode ter 100 destes sensores a gerar eventos como uma transmissão. O ideal seria ter um dispositivo de gateway que execute alguns códigos para enviar estes eventos para [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) ou [Hubs do Azure IoT](https://azure.microsoft.com/services/iot-hub/). A tarefa de Stream Analytics seria ingerir estes eventos a partir de Hubs de Eventos e executar consultas de análise em tempo real em relação aos fluxos. Em seguida, pode enviar os resultados para uma das [saídas suportadas](stream-analytics-define-outputs.md).

Para uma fácil utilização, neste manual de introdução, fornecemos-lhe um ficheiro de dados de exemplo, gerado a partir de dispositivos SensorTag reais, no qual pode executar diferentes consultas e ver os respetivos resultados. Nos tutoriais subsequentes, ficará a saber como ligar a tarefa às entradas e saídas e como as implementar no serviço do Azure.

## Criar tarefa do Stream Analytics
No [portal do Azure](http://manage.windowsazure.com), selecione o Stream Analytics e clique em **"Novo"** no canto inferior esquerdo da página para criar uma nova tarefa de análise.

![Criar uma nova tarefa do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

Clique em "**criação rápida**".

Para a definição **"Monitorização regional da conta do Storage"**, selecione **"Criar nova conta do Storage"** e atribua-lhe um nome exclusivo. O Azure Stream Analytics utiliza esta conta para armazenar informações de monitorização de todas as tarefas futuras.

> [!NOTE]
> Deve criar esta conta do Storage apenas uma vez por região e ela será partilhada em todas as tarefas do Stream Analytics que foram criadas nessa região.
> 
> 

Clique em "**Criar tarefa do Stream Analytics**" na parte inferior da página.

![Configuração da conta de armazenamento](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Consulta do Azure Stream Analytics
Clique no separador Consulta para aceder ao editor de consultas. O separador Consulta contém uma consulta T-SQL que efetua a transformação através dos dados de eventos recebidos.

## Arquivo de dados não processados
A forma mais simples de consulta é um pass-through que arquiva todos os dados de entrada para o resultado designado.

![Consulta de tarefa de arquivo](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Transfira agora o ficheiro de dados de exemplo a partir do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) para uma localização no computador. Copie e cole a consulta a partir do ficheiro **PassThrough.txt**. Clique no botão Teste em baixo e selecione o ficheiro de dados com o nome **HelloWorldASA InputStream.json** a partir da localização transferida.

![Botão Teste no Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![Fluxo de entrada de teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

Pode ver os resultados da consulta no browser, como indicado abaixo.

![Resultados do teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## Filtrar os dados com base numa condição
Vamos tentar filtrar os resultados com base numa condição. Gostaríamos de mostrar apenas os resultados provenientes do "SensorA". A consulta está localizada no ficheiro **Filtering.txt**.

![filtragem de um fluxo de dados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Repare que estamos a comparar um valor de cadeia e tem maiúsculas e minúsculas. Clique em **Voltar a executar** para executar a consulta. A consulta deverá devolver apenas 389 linhas de 1860 eventos.

![Segundos resultados de saída do teste de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## Alertas para acionar o fluxo de trabalho do negócio
Agora vamos tornar a nossa consulta mais detalhada. Para cada tipo de sensor, se quisermos monitorizar a temperatura média por cada janela de 30 segundos e apresentar os resultados apenas se a temperatura média for superior a 100 graus, vamos escrever a consulta abaixo e, em seguida, clicar em **Voltar a executar** para ver os resultados. A consulta está localizada no ficheiro **ThresholdAlerting.txt**.

![Consulta de filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Agora os resultados contêm apenas 245 linhas e apresentam os sensores cuja temperatura média é superior a 100. Nesta consulta agrupámos a transmissão de eventos por **dspl**, que é o Nome do Sensor, e colocámo-la numa **Janela em cascata** de 30 segundos. Quando realizamos estas consultas temporais, é essencial indicar como pretendemos que o tempo avance. Com a cláusula **TIMESTAMP BY**, especificámos a coluna "tempo" como uma forma de progresso do tempo para todos os cálculos temporais. Para obter informações detalhadas, leia os tópicos MSDN sobre [Gestão do tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [Modos de janela](https://msdn.microsoft.com/library/azure/dn835019.aspx).

![Temperatura superior a 100](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## Detetar a ausência de eventos
Como podemos escrever uma consulta para encontrar uma falta de eventos de entrada? É bastante fácil fazê-lo. Vamos descobrir quando foi a última vez que um sensor enviou dados e deixou de enviar eventos durante o minuto seguinte. A consulta está localizada no ficheiro **AbsenseOfEvent.txt**.

![Detetar a ausência de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Aqui vamos utilizar uma **ASSOCIAÇÃO EXTERNA** para a mesma transmissão de dados (associação automática). Para uma associação interna, só é apresentado um resultado quando é encontrada uma correspondência.  Mas numa associação **EXTERNA**, se um evento externo à associação não tiver qualquer correspondência, é apresentada a linha NULO para todas as colunas da linha à direita. Esta técnica é muito útil para localizar a ausência de eventos. Para obter mais informações sobre como [ADERIR](https://msdn.microsoft.com/library/azure/dn835026.aspx), consulte a nossa documentação MSDN.

![resultados da associação](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## Conclusão
O objetivo deste tutorial é demonstrar como escrever diferentes consultas de idioma de Stream Analytics e ver resultados no browser. No entanto, isto é apenas o início. O Stream Analytics tem muitas outras funcionalidades. O Stream Analytics suporta uma variedade de entradas e saídas e pode mesmo tirar partido de funções no Azure Machine Learning, tornando-a uma ferramenta robusta para analisar fluxos de dados. Pode explorar ainda mais o Stream Analytics através do nosso [Mapa de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/) e, para obter mais informações sobre como escrever consultas, leia o artigo sobre [Padrões de consulta comuns](stream-analytics-stream-analytics-query-patterns.md).

<!--HONumber=ago16_HO4-->


