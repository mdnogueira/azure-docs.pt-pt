---
title: "Recolher registos personalizados na análise de registos do OMS | Microsoft Docs"
description: "Análise de registos pode recolher eventos de ficheiros de texto em computadores Windows e Linux.  Este artigo descreve como definir um novo registo personalizado e detalhes dos registos que criarem no repositório de OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2017
ms.author: bwren
ms.openlocfilehash: addb1c8f4c71bb1979229c597665fd301dfb9fdf
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="custom-logs-in-log-analytics"></a>Registos personalizados na análise de registos
A origem de dados de registos personalizados na análise de registos permite-lhe recolher eventos de ficheiros de texto em computadores Windows e Linux. Muitas aplicações regista informações nos ficheiros de texto em vez dos serviços de registo padrão, tais como o registo de eventos do Windows ou Syslog.  Depois de recolhidos, pode analisar cada registo de início de sessão para os campos individuais utilizando o [campos personalizados](log-analytics-custom-fields.md) funcionalidade de análise de registos.

![Coleção de registo personalizado](media/log-analytics-data-sources-custom-logs/overview.png)

Os ficheiros de registo a recolher têm de corresponder aos seguintes critérios.

- O registo deve ter uma única entrada por linha ou utilizar um timestamp correspondente a um dos seguintes formatos no início de cada entrada.

    HH: MM: DE AAAA-MM-DD<br>M/YYYY HH: SS AM/PM <br>MON DD, hh: mm: de aaaa

- O ficheiro de registo não deve permitir atualizações circulares onde o ficheiro é substituído com novas entradas.
- O ficheiro de registo tem de utilizar codificação ASCII ou UTF-8.  Não são suportados outros formatos, tais como UTF-16.

>[!NOTE]
>Se existirem entradas duplicadas no ficheiro de registo, análise de registos irá recolher.  No entanto, os resultados da pesquisa será inconsistente onde os resultados do filtro mostram mais eventos que a contagem de resultados.  Será importante que valide o registo para determinar se a aplicação que cria este está a causar este comportamento e resolver-se possível antes de criar a definição de recolha de registo personalizado.  
>
  
## <a name="defining-a-custom-log"></a>Definir um registo personalizado
Utilize o procedimento seguinte para definir um ficheiro de registo personalizado.  Desloque-se ao fim deste artigo para obter instruções de uma amostra de adição de um registo personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Passo 1. Abrir o Assistente de registo personalizado
O Assistente de registo personalizado é executado no portal do OMS e permite-lhe definir um novo registo personalizado para recolher.

1. No portal do OMS, aceda a **definições**.
2. Clique em **dados** e, em seguida, **Custom logs**.
3. Por predefinição, todas as alterações de configuração são automaticamente enviadas por push para todos os agentes.  Para agentes Linux, é enviado um ficheiro de configuração para o recoletor de dados Fluentd.  Se pretender modificar este ficheiro manualmente em cada agente do Linux, em seguida, desmarque a caixa *aplicar configuração abaixo aos meus computadores Linux*.
4. Clique em **adicionar +** para abrir o Assistente de registo personalizado.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Passo 2. Carregar e analisar um registo de exemplo
Começar através do carregamento de uma amostra de registo personalizado.  O assistente irá analisar e apresentar as entradas neste ficheiro para que possa validar.  Análise de registos utilizará o delimitador que especificar para identificar cada registo.

**Nova linha** delimitador predefinido e é utilizada para os ficheiros de registo que tenham uma única entrada por linha.  Se a linha começa com uma data e hora dos formatos disponíveis, em seguida, pode especificar um **Timestamp** delimitador que suporta as entradas que abrangem mais do que uma linha.

Se for utilizado um delimitador de timestamp, a propriedade TimeGenerated de cada registo armazenado no OMS será preenchida com a data/hora especificada para essa entrada no ficheiro de registo.  Se for utilizado um delimitador de linha nova, em seguida, TimeGenerated é preenchido com a data e hora em que a análise de registos recolhidos a entrada.


1. Clique em **procurar** e navegue para um ficheiro de exemplo.  Tenha em atenção que isto pode botão poderá ser assinalada como **Escolher ficheiro** alguns browsers.
2. Clique em **Seguinte**.
3. O Assistente de registo personalizado irá carregar o ficheiro de lista e os registos que identifica.
4. Altere o delimitador utilizado para identificar um novo registo e selecione o delimitador melhor identifica os registos no ficheiro de registo.
5. Clique em **Seguinte**.

### <a name="step-3-add-log-collection-paths"></a>Passo 3. Adicionar caminhos de recolha de registos
Tem de definir um ou mais caminhos no agente onde o mesmo possa localizar o registo personalizado.  Pode optar por fornecer um caminho específico e um nome para o ficheiro de registo ou pode especificar um caminho com um caráter universal para o nome.  Isto suporta aplicações que criar um novo ficheiro de cada dia ou quando um ficheiro atinge um determinado tamanho.  Também pode fornecer vários caminhos para um único ficheiro de registo.

Por exemplo, uma aplicação poderá criar um ficheiro de registo por dia com a data incluída no nome que aparece log20100316.txt. Poderá ser um padrão para um registo de tal *registo\*. txt* que seria aplicada a qualquer ficheiro de registo após a aplicação da nomenclatura do esquema.

A tabela seguinte fornece exemplos de padrões válidos para especificar os diferentes ficheiros de registo.

| Descrição | Caminho |
|:--- |:--- |
| Todos os ficheiros na *C:\Logs* com extensão. txt no agente do Windows |C:\Logs\\\*. txt |
| Todos os ficheiros na *C:\Logs* com um nome começado com uma extensão. txt no agente do Windows e o registo |C:\Logs\log\*. txt |
| Todos os ficheiros na */var/log/audit* com extensão. txt no agente do Linux |/var/log/audit/*.txt |
| Todos os ficheiros na */var/log/audit* com um nome começado com uma extensão. txt no agente Linux e o registo |/var/log/audit/log\*. txt |

1. Selecione Windows ou Linux para especificar qual o formato de caminho que está a adicionar.
2. Escreva o caminho e clique em de  **+**  botão.
3. Repita o processo para qualquer caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Passo 4. Forneça um nome e descrição para o registo
O nome que especificar será utilizado para o tipo de registo, conforme descrito acima.  Sempre terminará com _CL distinguir como um registo personalizado.

1. Escreva um nome para o registo.  O  **\_CL** sufixo é fornecido automaticamente.
2. Adicionar opcional **Descrição**.
3. Clique em **seguinte** para guardar a definição de registo personalizado.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Passo 5. Validar que estão a ser recolhidos os registos personalizados
-Pode demorar uma hora para os dados iniciais de um novo registo de personalizada a aparecer na análise de registos.  Começará a recolher entradas de registos encontrados no caminho especificado do ponto de que definiu o registo personalizado.  Não manterá as entradas que carregou durante a criação de registo personalizado, mas irá recolher entradas já existentes nos ficheiros de registo que localiza.

Assim que for iniciada a análise de registos, recolher o registo personalizado, os respetivos registos estará disponíveis com uma pesquisa de registo.  Utilize o nome que deu o registo personalizado, como o **tipo** na sua consulta.

> [!NOTE]
> Se a propriedade de RawData está em falta a pesquisa, terá de fechar e reabrir o seu browser.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Passo 6. Analisar as entradas de registo personalizado
A entrada de registo inteira será armazenada numa única propriedade chamada **RawData**.  Irá provavelmente querer separar os diferentes tipos de informações em cada entrada para as propriedades individuais armazenadas no registo.  Fazê-lo utilizando o [campos personalizados](log-analytics-custom-fields.md) funcionalidade de análise de registos.

Os passos detalhados para analisar a entrada de registo personalizados não são fornecidos aqui.  Consulte o [campos personalizados](log-analytics-custom-fields.md) documentação para obter estas informações.

## <a name="disabling-a-custom-log"></a>Desativar um registo personalizado
Não é possível remover uma definição de registo personalizado quando for criado, mas pode desativá-lo ao remover todos os respetivos caminhos de recolha.

1. No portal do OMS, aceda a **definições**.
2. Clique em **dados** e, em seguida, **Custom logs**.
3. Clique em **detalhes** junto a definição de registo personalizados para desativar.
4. Remova todos os caminhos de coleção para a definição de registo personalizado.

## <a name="data-collection"></a>Recolha de dados
Análise de registos irão recolher novas entradas de cada registo personalizado aproximadamente a cada 5 minutos.  O agente irá registam seu lugar em cada ficheiro de registo que recolhe a partir de.  Se o agente fique offline durante um período de tempo, em seguida, análise de registos irão recolher entradas de onde pela última vez foi deixada, mesmo que essas entradas criadas enquanto o agente estava offline.

Todo o conteúdo da entrada de registo é escrito para uma única propriedade denominada **RawData**.  Pode analisar isto para várias propriedades que podem ser analisadas e pesquisadas separadamente, definindo [campos personalizados](log-analytics-custom-fields.md) depois de criar o registo personalizado.

## <a name="custom-log-record-properties"></a>Propriedades de registo de registo personalizado
Registos de registo personalizado tem um tipo com o nome de registo que fornecer e as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated |Data e hora em que o registo foi recolhido pelo registo de análise.  Se o registo utiliza um delimitador baseados no tempo, em seguida, este é o tempo recolhido a partir da entrada. |
| SourceSystem |Tipo de agente que foi recolhido o registo do. <br> Ligar OpsManager – o agente do Windows, é direta ou System Center Operations Manager <br> Linux – todos os agentes Linux |
| RawData |Texto completo da entrada de recolhidos. |
| ManagementGroupName |Nome do grupo de gestão para gerir o System Center Operations agentes.  Para outros agentes, o que é AOI -\<ID da área de trabalho\> |

## <a name="log-searches-with-custom-log-records"></a>Registo de pesquisas com registos de registo personalizado
Registos de registos personalizados são armazenados no repositório de OMS, tal como registos de qualquer outra origem de dados.  Terão um tipo correspondente ao nome que fornecem ao definir o registo de, pelo que pode utilizar a propriedade de tipo na sua pesquisa para obter registos recolhidos a partir de um registo específico.

A tabela seguinte fornece exemplos diferentes de pesquisas de registo que obter registos de registos personalizados.

| Consulta | Descrição |
|:--- |:--- |
| MyApp_CL |Todos os eventos de personalizadas MyApp_CL com o nome do registo. |
| MyApp_CL &#124; onde Severity_CF = = "erro" |Todos os eventos de personalizadas MyApp_CL com nome de registo com um valor de *erro* num campo personalizado denominado *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Instruções de exemplo da adição de um registo personalizado
A secção seguinte explica um exemplo de como criar um registo personalizado.  O registo de exemplo que está a ser recolhido tem uma única entrada em cada linha que começa com uma data e hora e, em seguida, delimitada por vírgulas campos para código, estado e a mensagem.  Várias entradas de exemplo são apresentadas abaixo.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Carregar e analisar um registo de exemplo
Iremos fornecer um dos ficheiros de registo e pode ver os eventos que irá ser recolhê-lo.  Neste caso, a nova linha é um delimitador suficiente.  Se uma única entrada no registo de foi abranger várias linhas, em seguida, seria necessário um delimitador de timestamp a ser utilizado.

![Carregar e analisar um registo de exemplo](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicionar caminhos de recolha de registos
Os ficheiros de registo irão estar localizados no *C:\MyApp\Logs*.  Será criado um novo ficheiro de cada dia com um nome que inclui a data no padrão de *appYYYYMMDD.log*.  Um padrão suficiente para este registo seria *C:\MyApp\Logs\\\*. log*.

![Caminho da coleção de registo](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Forneça um nome e descrição para o registo
Podemos utilizar um nome de *MyApp_CL* e escreva um **Descrição**.

![Nome do registo](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que estão a ser recolhidos os registos personalizados
Utilizamos uma consulta de *tipo = MyApp_CL* para devolver todos os registos do registo recolhido.

![Consulta de registo com nenhuma campos personalizados](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analisar as entradas de registo personalizado
Utilizamos os campos personalizados para definir o *EventTime*, *código*, *estado*, e *mensagem* campos e pode ver a diferença nos registos de que são devolvidos pela consulta.

![Consulta de registo com campos personalizados](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Passos seguintes
* Utilize [campos personalizados](log-analytics-custom-fields.md) para analisar as entradas no registo personalizado aos campos individuais.
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções.
