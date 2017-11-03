---
title: "Captura de descrição geral dos Event Hubs do Azure | Microsoft Docs"
description: Captura de dados de telemetria com capturar os Hubs de eventos
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: sethm;darosa
ms.openlocfilehash: c4fd365ec8eeb389f0df9f53cd2f2a18f4c9b52a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-event-hubs-capture"></a>Captura de Hubs de eventos do Azure

Captura de Hubs de eventos do Azure permitem entregar automaticamente os dados de transmissão em fluxo no Hubs de eventos para um [Blob storage do Azure](https://azure.microsoft.com/services/storage/blobs/) ou [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) conta à sua escolha, com a flexibilidade de adicionada especificar um intervalo de tempo ou tamanho. Configurar a captura é rápido, existem sem os custos administrativos executá-la e, se ajusta automaticamente com os Event Hubs [unidades de débito](event-hubs-features.md#capacity). Captura de Hubs de eventos é a forma mais fácil para carregar dados de transmissão em fluxo para o Azure e permite-lhe concentrar-se no processamento de dados em vez de na captura de dados.

Captura de Hubs de eventos permite-lhe processar pipelines com base em batch e em tempo real na mesma transmissão em fluxo. Isto significa que podem criar soluções que aumentam com necessidades ao longo do tempo. Se estiver a criar sistemas baseados em batch hoje com par para processamento futuro de em tempo real ou que pretende adicionar um caminho típico eficiente a uma solução existente em tempo real, capturar os Hubs de eventos faz com que trabalhar com dados mais fácil de transmissão em fluxo.

## <a name="how-event-hubs-capture-works"></a>Como funciona a captura de Hubs de eventos

Os Event Hubs são uma tempo de retenção durável memória intermédia para a entrada de telemetria, semelhante a um registo distribuído. A chave para dimensionamento em Event Hubs é o [modelo de consumidor particionado](event-hubs-features.md#partitions). Cada partição é um segmento de dados independente e é consumida de forma independente. Ao longo do tempo este sua idade aumenta desligada, com base no período de retenção configurável. Como resultado, um hub de eventos fornecido nunca obtém "demasiado completo".

Captura de Hubs de eventos permite-lhe especificar a sua própria conta de armazenamento de Blobs do Azure e um contentor ou uma conta do Azure Data Lake Store, que são utilizados para armazenar os dados capturados. Estas contas podem ser na mesma região que o hub de eventos ou noutra região, adicionar a flexibilidade da funcionalidade de captura de Hubs de eventos.

Dados capturados são escritos em [Apache Avro] [ Apache Avro] formato: um formato compacto, rápido, binário, que fornece as estruturas de dados formatado com o esquema inline. Este formato é amplamente utilizado o ecossistema Hadoop, o Stream Analytics e o Azure Data Factory. Obter mais informações sobre como trabalhar com Avro estão disponíveis neste artigo.

### <a name="capture-windowing"></a>Capturar em modo de janela

Captura de Hubs de eventos permite-lhe configurar uma janela para controlar a capturar. Esta janela é um tamanho mínimo e a configuração de tempo com "primeira wins, uma política de", o que significa que o primeiro acionador encontrado faz com que uma operação de captura. Se tiver um introdução de quinze minutos, 100 MB capturar janela e a enviar de 1 MB por segundo, os acionadores de janela de tamanho antes da janela de tempo. Cada partição capturas de forma independente e escreve um blob de blocos foi concluída no momento da captura, com o nome para a hora em que o intervalo de captura foi encontrado. A Convenção de nomenclatura de armazenamento é o seguinte:

```
[namespace]/[event hub]/[partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>Dimensionamento para unidades de débito

Tráfego de Hubs de eventos é controlado pelo [unidades de débito](event-hubs-features.md#capacity). Uma única unidade de débito permite 1 MB por segundo ou 1000 eventos por segundo de entrada e de duas vezes que quantidade de saída. Hubs de eventos Standard pode ser configurados com 1-20 unidades de débito e pode comprar mais com uma quota aumentar [pedido de suporte][support request]. Utilização para além das unidades de débito adquiridas é limitada. Captura de Hubs de eventos copia os dados diretamente a partir do armazenamento de Event Hubs interno, ignorando as quotas de saída de unidade de débito e guardar a saída para outros leitores de processamento, por exemplo, o Stream Analytics ou Spark.

Depois de configurar, capturar os Hubs de eventos é executado automaticamente quando enviar o primeiro evento e continua a ser executado. Para tornar mais fácil para o processamento a jusante saber que o processo está a funcionar, os Event Hubs ficheiros vazios quando escreve não contém dados. Este processo fornece uma cadência previsível e marcador que pode atribuir os processadores do batch.

## <a name="setting-up-event-hubs-capture"></a>Como configurar a captura de Hubs de eventos

Pode configurar captura, a utilização do tempo de criação do hub de eventos de [portal do Azure](https://portal.azure.com), ou utilizando os modelos Azure Resource Manager. Para obter mais informações, veja os artigos seguintes:

- [Ativar a captura de Hubs de eventos no portal do Azure](event-hubs-capture-enable-through-portal.md)
- [Criar um espaço de nomes de Hubs de eventos com um hub de eventos e ativar a captura utilizando um modelo Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os ficheiros capturados e trabalhar com Avro

Hubs de eventos captura cria ficheiros no formato Avro, conforme especificado na janela de tempo configurado. Pode ver estes ficheiros em qualquer ferramenta tais como [Explorador de armazenamento do Azure][Azure Storage Explorer]. Pode transferir os ficheiros localmente para trabalhar nos mesmos.

Os ficheiros produzidos pela captura de Hubs de eventos têm o esquema Avro seguinte:

![][3]

Uma forma fácil para explorar os ficheiros do Avro é utilizando o [Avro ferramentas] [ Avro Tools] jar do Apache. Depois de transferir este jar, pode ver o esquema de um ficheiro Avro específico, executando o seguinte comando:

```
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Este comando devolve

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Também pode utilizar ferramentas de Avro para converter o ficheiro em formato JSON e executar outro processamento.

Para efetuar o processamento de mais avançado, transfira e instale Avro para a escolha da plataforma. No momento desta redação, existem as implementações disponíveis para C, C++, C\#, Java, NodeJS, Perl, PHP, Python e Ruby.

Apache Avro tem concluídos guias de introdução para [Java] [ Java] e [Python][Python]. Também pode ler o [introdução ao Event Hubs capturar](event-hubs-capture-python.md) artigo.

## <a name="how-event-hubs-capture-is-charged"></a>Como capturar os Hubs de eventos é cobrada

Captura de Hubs de eventos é limitado da mesma forma para unidades de débito: como um custo por hora. A taxa é diretamente proporcional ao número de unidades de débito adquiridas para o espaço de nomes. Unidades de débito são aumentar e diminuir, medidores capturar os Hubs de eventos aumentam e diminuir a proporcionar um desempenho correspondente. Os medidores ocorrerem em conjunto. Para detalhes de preços, consulte [preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>Passos seguintes

Captura de Hubs de eventos é a forma mais fácil para obter os dados no Azure. Utilizar o Azure Data Lake, o Azure Data Factory e o Azure HDInsight, pode realizar processamento em lote e outros analytics com ferramentas familiares e plataformas à sua escolha, em qualquer escala precisa.

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Começar a enviar e receber eventos](event-hubs-dotnet-framework-getstarted-send.md)
* [Descrição geral dos Hubs de Eventos][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
