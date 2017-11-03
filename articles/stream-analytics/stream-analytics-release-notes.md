---
title: "Notas de versão do Stream Analytics | Microsoft Docs"
description: "Notas de versão do Stream Analytics"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/03/2017
ms.author: samacha
ms.openlocfilehash: 3251cd47bb917912d63330345dbf392e724448ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-release-notes"></a>Notas de versão do Stream Analytics

## <a name="notes-for-06142017-update-of-stream-analytics-tools-for-visual-studio"></a>Notas de atualização de 14/06/2017 do Stream Analytics Tools para Visual Studio
Esta atualização destina-se a nossa Visual Studio Tools. Esta versão contém as seguintes novas funcionalidades:

| Título | Descrição |
| --- | --- |
| Suporte de editor de scripts de Java |Possam desfrutar a experiência de editor de scripts de java nativo depois de criar o java funções de script.|
| Mensagem de erro de tempo de execução da tarefa vista | Se existirem erros de runtime durante a execução da tarefa, pode visualizá-los no separador de erros ao ajustar o intervalo de tempo de apresentação. Por predefinição mostra as mensagens de erro para os últimos 30 minutos. |
| Suporte CSV e Avro para entrada de teste local | Para além de JSON, agora pode utilizar o formato de ficheiro CSV e Avro para a entrada de teste local.|

## <a name="notes-for-05032017-update-of-stream-analytics"></a>Notas para atualização 03/05/2017 do Stream Analytics
Esta atualização é a nossa versão de documentação de resolução de problemas.

O [guia de resolução de problemas](stream-analytics-troubleshooting-guide.md) e foram lançados outros documentos. Reveja, comentários boas-vindas.

## <a name="notes-for-04242017-update-of-stream-analytics-tools-for-visual-studio"></a>Notas de atualização de 24/04/2017 do Stream Analytics Tools para Visual Studio
Esta atualização destina-se a nossa Visual Studio Tools. Esta versão contém as seguintes novas funcionalidades:

| Título | Descrição |
| --- | --- |
| Ver o resultado do teste local no Visual Studio | Para ver o resultado de saída do teste local, prima ENTER na janela da consola de saída ou fechá-lo. O resultado é apresentado numa janela no Visual Studio no formato de tabela. |
| Resultado do local de saída no formato JSON | Quando executar um teste local, o resultado de saída é gerado como formatos de ficheiro JSON e CSV. |
| Pré-visualize os dados de entradas/saídas de armazenamento do BLOBs/tabela | Fazendo duplo clique no armazenamento de BLOBs ou tabela de entrada/saída na vista de tarefa, pode pré-visualizar os dados no interior do Visual Studio facilmente. |
| Mensagem de erro de vista para entradas/saídas | Se existirem alguns erros de runtime relacionadas com a sua tarefa entradas e saídas, é mostrado no diagrama tarefa onde pode passar na mesma para ver a mensagem de erro detalhadas.|


## <a name="notes-for-02012017-release-of-stream-analytics"></a>Notas de versão de 01/02/2017 do Stream Analytics
Esta versão contém a seguinte atualização:

| Título | Descrição |
| --- | --- |
| Introdução às funções definidas pelo utilizador JavaScript (UDF) |[As funções definidas pelo utilizador de Java](stream-analytics-javascript-user-defined-functions.md) estão agora disponíveis para flexibilidade adicional na criação de consultas. |
| Introdução às ferramentas do Visual Studio e o Stream Analytics |[Ferramentas para Visual Studio](stream-analytics-tools-for-visual-studio.md) estão agora disponíveis para o utilitário de depuração e superior. |
| Introdução ao registo de diagnóstico |[Registo de diagnóstico](stream-analytics-job-diagnostic-logs.md) está agora disponível para opções de resolução de problemas adicionais. |
| Introdução às funções de Geoespacial |[Funções de Geoespacial](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx) agora são geralmente disponível. |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Notas de versão de 04/15/2016 do Stream Analytics
Esta versão contém a seguinte atualização:

| Título | Descrição |
| --- | --- |
| Disponibilidade geral para saídas de Power BI |[Power BI saídas](stream-analytics-power-bi-dashboard.md) agora são geralmente disponível. A expiração de autorização de 90 dias do Power BI foi removida. Para obter mais informações sobre os cenários em que tem de ser renovado de autorização, consulte o [renovar autorização](stream-analytics-power-bi-dashboard.md#renew-authorization) secção de criação de um dashboard do Power BI. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Notas de versão de 03/03/2016 do Stream Analytics
Esta versão contém a seguinte atualização:

| Título | Descrição |
| --- | --- |
| Novos itens de idioma de consulta do Stream Analytics |Tem agora SAQL [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN página"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN página") e [REGEXMATCH] (https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN página"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Notas de versão 12/10/2015 do Stream Analytics
Esta versão contém a seguinte atualização:

| Título | Descrição |
| --- | --- |
| Atualização da versão de REST API |A versão da API de REST foi atualizada para 2015-10-01. Podem ser encontrados detalhes no MSDN em [referência de API do REST de gestão do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx) e [integração de Machine Learning no Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Integração do Azure Machine Learning |Com esta versão inclui suporte para o Azure Machine Learning funções definidas pelo utilizador. Consulte o [tutorial](stream-analytics-machine-learning-integration-tutorial.md) para obter mais informações, bem como o [blogue geral anúncio](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Notas de versão de 11/12/2015 do Stream Analytics
Esta versão contém a seguinte atualização:

| Título | Descrição |
| --- | --- |
| Novo comportamento de SELECIONE |SELECIONE no Stream Analytics foi expandido para permitir * como um acessor de propriedade de um registo aninhado. Para obter mais informações, consulte [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "tipos de dados complexos"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Notas de versão de 22/10/2015 do Stream Analytics
Esta versão contém as seguintes atualizações:

| Título | Descrição |
| --- | --- |
| Funcionalidades de idioma de consulta adicionais |Do Stream Analytics expandiu o idioma de consulta, incluindo as seguintes funcionalidades: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [limite](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [piso](https://msdn.microsoft.com/library/azure/mt605240.aspx), [ ENERGIA](https://msdn.microsoft.com/library/azure/mt605287.aspx), [sessão](https://msdn.microsoft.com/library/azure/mt605290.aspx), [QUADRADA](https://msdn.microsoft.com/library/azure/mt605288.aspx), e [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Limitações de agregação removidas |Esta versão remove a limitação de 15 agregados numa consulta. Agora, não há nenhum limite para o número de agregados por consulta. |
| GRUPO por Timestamp funcionalidades |O [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) função agora permite o window_type ou [Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| DESVIO adicionado de salto windows e em cascata |Por predefinição, [em cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) e [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) windows estão alinhado com tempo (1/1/0001 e UTC 12:00:00: 00). O novo parâmetro (opcional) 'offsetsize' permite a especificação de um desvio personalizado (ou alinhamento). |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Notas de versão de 29/09/2015 do Stream Analytics
Esta versão contém as seguintes atualizações:

| Título | Descrição |
| --- | --- |
| Pré-visualização pública do Azure IoT Suite |Do Stream Analytics está incluído na pré-visualização pública do Azure IoT Suite. |
| Integração do portal do Azure |Para além da presença contínua no portal do Azure, o Stream Analytics agora está integrado no [portal do Azure](https://azure.microsoft.com/overview/preview-portal/). Tenha em atenção que a funcionalidade de Stream Analytics no portal de pré-visualização está atualmente a que um subconjunto das funcionalidades oferecidas no portal do Azure, sem suporte para o teste de consulta no browser, que configuração e navegando até ou criar nova entrada e saída de saída do Power BI recursos em subscrições que tem acesso. |
| Suporte para o resultado da BD do Cosmos |Tarefas do Stream Analytics podem agora exportar para [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). |
| Suporte para a entrada do IoT Hub |Tarefas do Stream Analytics agora podem ingerir dados a partir de IoT Hubs. |
| TIMESTAMP BY para eventos heterogéneos |Quando um fluxo de dados único contém vários tipos de eventos ter carimbos nos campos diferentes, agora, pode utilizar [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) com expressões para especificar os campos de timestamp diferente para cada caso. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Notas de versão 10/09/2015 do Stream Analytics
Esta versão contém as seguintes atualizações:

| Título | Descrição |
| --- | --- |
| Suporte para grupos de PowerBI |Para ativar a partilha de dados com outros utilizadores do Power BI, tarefas do Stream Analytics podem agora escrever [PowerBI grupos](stream-analytics-define-outputs.md#power-bi) dentro da sua conta do Power BI. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Notas de versão de 20/08/2015 do Stream Analytics
Esta versão contém as seguintes atualizações:

| Título | Descrição |
| --- | --- |
| Adicionar a última função |O [último](http://msdn.microsoft.com/library/mt421186.aspx) função está agora disponível em tarefas do Stream Analytics, permitindo-lhe obter o evento mais recente num fluxo de eventos durante um período de tempo especificado. |
| Novas funções de matriz |As funções de matriz [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) e [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) estão agora disponíveis. |
| Novas funções de registo |As funções de registo [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) e [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) estão agora disponíveis. |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Notas de versão de 30/07/2015 do Stream Analytics
Esta versão contém as seguintes atualizações:

| Título | Descrição |
| --- | --- |
| Id de organização do Power BI dissociado da Id do Azure |Esta funcionalidade permite [saída do Power BI](stream-analytics-power-bi-dashboard.md) para tarefas do ASA em qualquer tipo de conta do Azure (Live Id ou Id de organização). Além disso, pode ter um Id de organização para a sua conta do Azure e utilizar um diferente para autorizar a saída do Power BI. |
| Suporte para o resultado de filas do Service Bus |[Filas do Service Bus](stream-analytics-define-outputs.md#service-bus-queues) saídas estão agora disponíveis em tarefas do Stream Analytics. |
| Suporte para o resultado de tópicos de Service Bus |[Tópicos de Service Bus](stream-analytics-define-outputs.md#service-bus-topics) saídas estão agora disponíveis em tarefas do Stream Analytics. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Notas de versão de 07/09/2015 do Stream Analytics
Esta versão contém as seguintes atualizações:

| Título | Descrição |
| --- | --- |
| Blob de personalizada a criação de partições de saída |Saídas de armazenamento de BLOBs permitem agora uma opção para especificar a frequência essa saída blobs são escritos e a estrutura e o formato da estrutura de pasta do caminho de dados de saída. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Notas de versão de 05/03/2015 do Stream Analytics
Esta versão contém as seguintes atualizações:

| Título | Descrição |
| --- | --- |
| Maior valor máximo durante um período de tolerância fora de ordem |O tamanho máximo para o período de tolerância fora de ordem está agora 59:59 (mm: SS) |
| Formato de saída JSON: Linha separados ou a matriz |Agora há uma opção quando exportar para armazenamento de BLOBs ou o Hub de eventos de saída como uma matriz de objetos JSON ou separando objetos JSON com uma nova linha. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Notas de versão de 16/04/2015 do Stream Analytics
| Título | Descrição |
| --- | --- |
| Atraso na configuração da conta de armazenamento do Azure |Ao criar uma tarefa de Stream Analytics numa região pela primeira vez, lhe for pedido para criar uma nova conta de armazenamento ou especificar uma conta existente para a monitorização de tarefas do Stream Analytics nessa região. Devido à latência na configuração da monitorização, criar outra tarefa de Stream Analytics na mesma região dentro de 30 minutos a linha de comandos para a especificação de uma segunda conta de armazenamento em vez de mostrar dos configurado recentemente na lista pendente de conta de armazenamento de monitorização. Para evitar a criação de uma conta de armazenamento desnecessária, aguarde 30 minutos depois de criar uma tarefa numa região pela primeira vez antes do aprovisionamento tarefas adicionais nessa região. |
| Tarefa de atualização |Neste momento, o Stream Analytics não suporta edições em direto para a configuração de uma tarefa em execução ou a definição. Para alterar a entrada, saída, consulta, escala ou a configuração de uma tarefa em execução, tem primeiro de parar a tarefa. |
| Tipos de dados inferidos a partir da origem de entrada |Se não for utilizada uma instrução CREATE TABLE, o tipo de entrada é derivado de formato de entrada, por exemplo todos os campos do ficheiro CSV são cadeia. Campos têm de ser convertida explicitamente o tipo correcto utilizando a função de conversão para evitar erros de falta de correspondência de tipo. |
| Os campos em falta são debitados como valores nulos |Referencia um campo que não está presente na origem de entrada resulta em valores nulos existentes no evento de saída. |
| COM as instruções, tem de preceder o instruções SELECT |Na sua consulta, instruções SELECT tem de seguir as subconsultas definidas com as instruções. |
| Problema de memória esgotada |Reinicie as tarefas de análise de transmissão em fluxo com base numa tolerância grande para fora de ordem eventos e/ou uma grande quantidade de estado pode fazer com que a tarefa para ser executada sem memória, resultando numa tarefa de manutenção de consultas complexas. As operações de início e paragem estão visíveis nos registos de operação da tarefa. Para evitar este comportamento, escalamento horizontal a consulta várias partições. Numa versão futura, esta limitação é abordada degradar o desempenho das tarefas afetados em vez de reiniciá-los de. |
| Entradas de grande blob sem payload timestamp podem provocar um problema de memória esgotada |Consumir grandes ficheiros a partir do Blob storage pode fazer com que as tarefas do Stream Analytics para falha se um campo timestamp não for especificado através de TIMESTAMP BY. Para evitar este problema, mantenha cada blob em 10 MB de tamanho. |
| Limitação de volume de eventos de base de dados SQL |Ao utilizar a base de dados do SQL Server como um destino de saída, muito elevados volumes de dados de saída podem fazer com que a tarefa de Stream Analytics para o tempo limite. Para resolver este problema, reduzir o volume de saída através da utilização de agregados ou operadores de filtro ou escolha armazenamento de Blobs do Azure ou Event Hubs como um destino de saída em vez disso. |
| Conjuntos de dados do Power BI só podem conter uma tabela |Power BI não suporta mais de uma tabela num conjunto de dados indicado. |

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
