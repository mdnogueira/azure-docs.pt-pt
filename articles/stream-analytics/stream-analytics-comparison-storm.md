---
title: "Plataformas de análise: comparação do Apache Storm Stream Analytics | Microsoft Docs"
description: "Obter a documentação de orientação escolher uma plataforma de análise em nuvem, utilizando uma comparação do Apache Storm Stream Analytics. Compreenda as funcionalidades e as diferenças."
keywords: "plataforma de análise, as plataformas de análise, plataforma de análise em nuvem, a comparação de storm"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: samacha
ms.openlocfilehash: 97044cb5d7b0b3fcb3b85328df618a265bc59b61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Escolher uma plataforma de análise de transmissão em fluxo: comparar o Apache Storm e o Azure Stream Analytics
O Azure oferece várias soluções para analisar dados de transmissão em fluxo: [análise do Azure de transmissão em fluxo](https://docs.microsoft.com/azure/stream-analytics/) e [Apache Storm no Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-storm/). Ambas as plataformas de análise de fornecer as vantagens de uma solução de PaaS. Mas as plataformas tem algumas diferenças significativas nas respetivas capacidades, bem como no como configurar e geri-los. 

Este artigo fornece uma comparação lado a lado das funcionalidades para o ajudar a escolher entre o Apache Storm e do Azure Stream Analytics como uma plataforma de análise em nuvem. 

## <a name="general-features"></a>Funcionalidades gerais

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Do Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Abrir a origem?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não. O Azure Stream Analytics é um proprietários da Microsoft da oferta.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim. O Apache Storm é uma tecnologia do Apache licenciado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte da Microsoft?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisitos de hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
nenhum. O Azure Stream Analytics é um serviço do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
nenhum. O Apache Storm é um serviço do Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unidade de nível superior</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os utilizadores implementam e monitorizar tarefas de transmissão em fluxo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os utilizadores implementam e monitorizar um cluster de todo, que pode alojar várias tarefas de Storm, bem como outras cargas de trabalho (incluindo o batch).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Preços</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Um preço por volume do processamento de dados e o número de unidades de transmissão em fluxo necessário por hora que esteja a executar a tarefa. 
                </p>
                    <p>Para obter mais informações, consulte <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">preços do Stream Analytics</a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
A unidade de compra é baseado em cluster e é cobrada com base na hora que o cluster está em execução, independente de tarefas implementadas.
                </p>
                <p>
Para obter mais informações, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/">preços do HDInsight</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Criação

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Do Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacidades: SQL DSL?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim. Do Stream Analytics fornece uma linguagem como o SQL Server para criar transformações.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não. Os utilizadores escrever código Java ou c# ou utilizam Trident APIs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacidades: Os operadores temporais?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
As agregações e associações temporais são suportadas por predefinição.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Operadores temporais tem de ser implementados pelo utilizador.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Experiência de desenvolvimento</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os utilizadores podem criar, depurar e monitorizar tarefas através do portal do Azure, utilizando os dados de exemplo derivados de uma transmissão em fluxo em direto.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os utilizadores através do .NET podem desenvolver, depurar e monitorizar através do Visual Studio. Os utilizadores com o Java ou outros idiomas podem utilizar o IDE à escolha.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte de depuração</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os registos de operações e o estado da tarefa básica estão disponíveis para ajudar a depurar. Análise de fluxo atualmente não permitir que os utilizadores especificar o conteúdo ou a quantidade de conteúdo está incluído nos registos (ou seja, modo verboso).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Estão disponíveis registos detalhados. Os utilizadores podem aceder a registos no Visual Studio ou ao iniciar sessão para o cluster e aceder diretamente aos registos.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensibilidade utilizando código personalizado?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Suporta parcialmente com JavaScript UDFs. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">integração UDF do JavaScript</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim. Os utilizadores podem escrever código personalizado em c#, Java ou qualquer outro idioma suportado Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Origens de dados (entradas) e saídas ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Do Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Origens de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Armazenamento de Blobs do Azure e Hubs de eventos do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os conectores estão disponíveis para os Event Hubs do Azure, Azure Service Bus, Kafka e muito mais. Os utilizadores podem criar conectores adicionais utilizando código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Os utilizadores podem implementar qualquer formato utilizando código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Saídas</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Uma tarefa de transmissão em fluxo pode ter várias saídas. Saídas suportadas são Event Hubs do Azure, Blob do Azure armazenamento, Azure Table storage, BD SQL do Azure e Power BI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm suporta várias saídas numa topologia e cada saída pode ter uma lógica personalizada para o processamento a jusante. Storm inclui conectores para o Power BI, Event Hubs do Azure, Blob do Azure do armazenamento, base de dados do Azure Cosmos, SQL e HBase. Os utilizadores podem criar conectores adicionais utilizando código personalizado.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de codificação de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dados tem de ser formatados utilizando UTF-8.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Os utilizadores podem implementar qualquer formato de codificação de dados utilizando código personalizado.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Gestão e operações ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Do Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modelo de implementação da tarefa</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Portal do Azure, PowerShell e REST APIs.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Portal do Azure, PowerShell, Visual Studio e REST APIs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitorização (estatísticas, contadores, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Monitorização é implementada com o portal do Azure e REST APIs. Os utilizadores também podem configurar alertas do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Monitorização é implementada utilizando a IU do Storm e REST APIs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Escalabilidade</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Escalabilidade é determinada pelo número de unidades de transmissão em fluxo (SUs) para cada tarefa. Cada unidade de transmissão em fluxo processa até 1 MB por segundo, com um máximo unidades 50. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">escala para aumentar o débito</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Escalabilidade é determinada pelo número de nós no cluster de HDInsight Storm. O limite superior no número de nós é definido pela quota do Azure do utilizador.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites de processamento de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os utilizadores podem aumentar o processamento de dados ou otimizar os custos ao aumentar ou reduzir o número de unidades de transmissão em fluxo, com um limite superior de 1 GB por segundo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os utilizadores podem aumentar o tamanho de cluster ou reduzir verticalmente.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Parar/retomar</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Parar e retomar a partir do último lugar parado.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Parar e retomar a partir da última colocar parado com base numa marca d'água.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Atualização de serviço e a arquitetura</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Correção automática sem período de indisponibilidade.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Correção automática sem período de indisponibilidade.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuidade do negócio através de um serviço de elevada disponibilidade com SLAs garantidos</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>SLA de 99,9% tempo de atividade</li>
                <li>Recuperação automática de falhas</li>
                <li>Recuperação incorporadas do operadores temporais com monitorização de estado</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
SLA de 99,9% de disponibilidade do Storm cluster. 
                </p>
                <p>
O Apache Storm é uma plataforma de transmissão em fluxo com tolerância a falhas. No entanto, é da responsabilidade do utilizador para garantir que as tarefas de transmissão em fluxo executam interrompidas.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Funcionalidades avançadas ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Do Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Processamento de eventos de chegada e fora de ordem dinâmico</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Políticas configuráveis incorporadas podem reordenar os eventos, remova eventos ou ajuste o tempo do evento.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os utilizadores tem de implementar lógica para lidar com este cenário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dados de referência</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dados de referência estão disponíveis a partir do Blob storage do Azure com um máximo de 100 MB da cache na memória. Dados de referência são atualizados pelo serviço.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não existem limites de tamanho de dados. Os conectores estão disponíveis para o HBase, base de dados do Azure Cosmos, SQL Server e do Azure. Os utilizadores podem criar conectores adicionais utilizando código personalizado. Dados de referência tem de ser atualizados utilizando código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integração com o Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Publicar modelos podem ser configurados como as funções durante a criação da tarefa do Azure Machine Learning. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">escala para funções de Machine Learning</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Está disponível através do Storm Bolts.
                </p>
            </td>
        </tr>
    </tbody>
</table>
