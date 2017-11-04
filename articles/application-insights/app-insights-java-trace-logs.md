---
title: Explorar Java registos de rastreio no Azure Application Insights | Microsoft Docs
description: Rastreios de pesquisa Log4J ou Logback no Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: mbullwin
ms.openlocfilehash: 6e441c9cbd15bb1528ea8e8a781f90900af90cf2
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Explorar Java registos de rastreio no Application Insights
Se estiver a utilizar Logback ou Log4J (v1.2 ou v 2.0) para o rastreio, pode fazer com que os registos de rastreio automaticamente enviados para o Application Insights, onde pode explorar e procurar nos mesmos.

## <a name="install-the-java-sdk"></a>Instalar o Java SDK

Instalar [Application Insights SDK para Java][java], se ainda não o tiver feito que.

(Se não pretender monitorizar os pedidos HTTP, pode omitir a maior parte do ficheiro de configuração. XML, mas tem de incluir, pelo menos, o `InstrumentationKey` elemento. Também deve chamar `new TelemetryClient()` para inicializar o SDK.)


## <a name="add-logging-libraries-to-your-project"></a>Adicionar bibliotecas de registo ao seu projeto
*Escolha a forma adequada para o seu projeto.*

#### <a name="if-youre-using-maven"></a>Se estiver a utilizar Maven...
Se o seu projeto já está configurado para utilizar Maven para a compilação, intercale um os fragmentos de código seguintes no ficheiro pom.xml.

Em seguida, Atualize as dependências do projeto, para obter os binários transferidos.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*V 2.0 de Log4J*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Se estiver a utilizar Gradle...
Se o seu projeto já está configurado para utilizar Gradle para a compilação, uma das seguintes linhas ao adicionar o `dependencies` grupo no seu ficheiro gradle:

Em seguida, Atualize as dependências do projeto, para obter os binários transferidos.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '1.0.+'
```

**V 2.0 de Log4J**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '1.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '1.0.+'
```

#### <a name="otherwise-"></a>Caso contrário...
Transferir e extrair o appender adequada e, em seguida, adicionar a biblioteca adequada ao seu projeto:

| Registo | Transferência | Biblioteca |
| --- | --- | --- |
| Logback |[SDK com Logback appender](https://aka.ms/xt62a4) |applicationinsights-registo-logback |
| V 2.0 de Log4J |[SDK com Log4J v2 appender](https://aka.ms/qypznq) |applicationinsights-registo-log4j2 |
| Log4j v1.2 |[SDK com Log4J v1.2 appender](https://aka.ms/ky9cbo) |applicationinsights-registo-log4j1_2 |

## <a name="add-the-appender-to-your-logging-framework"></a>Adicionar o appender à sua arquitetura de registo
Para iniciar a obtenção de rastreios, intercale o fragmento de código para o ficheiro de configuração Log4J ou Logback relevante: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*V 2.0 de Log4J*

```XML

    <Configuration packages="com.microsoft.applicationinsights.Log4j">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Os appenders Application Insights podem ser referenciadas por qualquer registo configurado e não necessariamente pelo registo de raiz (conforme ilustrado nos exemplos de código acima).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Explorar os rastreios no portal do Application Insights
Agora que configurou o seu projeto para enviar os rastreios ao Application Insights, pode ver e procurar estes rastreios no portal do Application Insights, o [pesquisa] [ diagnostic] painel.

![No portal do Application Insights, abra pesquisa](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>Passos seguintes
[Pesquisa de diagnóstico][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md


