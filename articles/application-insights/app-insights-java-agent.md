---
title: "Monitorização de desempenho para aplicações web de Java no Azure Application Insights | Microsoft Docs"
description: "Expandida monitorização de desempenho e utilização do seu site em Java com o Application Insights."
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: ecfcf7a3b3698435f98b74474d0ca7223ab2b46c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Monitorizar dependências, exceções e os tempos de execução nas web apps do Java


Se tiver [instrumentados a sua aplicação web de Java com o Application Insights][java], pode utilizar o agente Java para obter informações mais aprofundadas, sem quaisquer alterações de código:

* **Dependências:** dados sobre chamadas que torna a sua aplicação para outros componentes, incluindo:
  * **Chamadas REST** efetuadas através de HttpClient, OkHttp e RestTemplate (mola).
  * **Redis** chamadas efetuadas através do cliente Jedis. Se a chamada demora mais que 10s, o agente também obtém os argumentos de chamada.
  * **[Chamadas JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB ou Apache Derby DB. chamadas de "executeBatch" são suportadas. Para o MySQL e PostgreSQL, se a chamada demora mais que 10s, o agente reporta o plano de consulta.
* **Detetada exceções:** dados sobre as exceções que são processados pelo seu código.
* **Tempo de execução do método:** dados sobre o tempo que demora a executar métodos específicos.

Para utilizar o agente Java, instalá-lo no seu servidor. As suas aplicações web têm de ser equipadas com o [Application Insights SDK de Java][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalar o agente do Application Insights para Java
1. No computador a executar o servidor de Java [transferir o agente](https://aka.ms/aijavasdk).
2. Edite o script de arranque do servidor de aplicação e adicione as JVM seguinte:
   
    `javaagent:`*caminho completo para o ficheiro JAR do agente*
   
    Por exemplo, no Tomcat num computador Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie o servidor de aplicação.

## <a name="configure-the-agent"></a>Configurar o agente
Crie um ficheiro denominado `AI-Agent.xml` e colocá-lo na mesma pasta que o ficheiro JAR do agente.

Defina o conteúdo do ficheiro xml. Edite o exemplo a seguir para incluir ou omita as funcionalidades que pretende.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Tem de permitir exceção de relatórios e a temporização de método para métodos individuais.

Por predefinição, `reportExecutionTime` é verdadeiro e `reportCaughtExceptions` é falso.

## <a name="view-the-data"></a>Ver os dados
No recurso do Application Insights, aparece agregados remotos dependência e método os tempos de execução [em mosaico desempenho][metrics].

Para procurar instâncias individuais da dependência, exceções e método relatórios, abra [pesquisa][diagnostic].

[Diagnosticar problemas de dependência - Saiba mais](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Tem dúvidas? Problemas?
* Não existem dados? [Exceções de firewall de conjunto](app-insights-ip-addresses.md)
* [Resolução de problemas de Java](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
