---
title: "Análise de aplicação Web em Java com o Application Insights do Azure | Microsoft Docs"
description: "Monitorização do Desempenho de Aplicações para aplicações Web Java com o Application Insights. "
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 99c9740e3f19e2a09332317b08e06352ffa8eee7
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Introdução ao Application Insights num projeto Web em Java


O [Application Insights](https://azure.microsoft.com/services/application-insights/) é um serviço de análise extensível para programadores Web que os ajudam a compreender o desempenho e a utilização da aplicação em direto. Utilize-o para [detetar e a diagnosticar problemas de desempenho e exceções](app-insights-detect-triage-diagnose.md), e [escrever código][api] para controlar o que os utilizadores fazem com a sua aplicação.

![dados de exemplo](./media/app-insights-java-get-started/5-results.png)

O Application Insights suporta aplicações em Java em execução no Linux, Unix ou Windows.

É necessário:

* Oracle JRE 1.6 ou posterior, ou Zulu JRE 1.6 ou posterior
* Uma subscrição do [Microsoft Azure](https://azure.microsoft.com/).

*Se tiver uma aplicação Web que já está em direto, pode seguir o procedimento alternativo para [adicionar o SDK no tempo de execução ao servidor Web](app-insights-java-live.md). Essa alternativa evita a reconstrução do código, mas não tem a opção para escrever código de modo a controlar a atividade do utilizador.*

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obter uma chave de instrumentação do Application Insights
1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).
2. Crie um recurso do Application Insights. Defina o tipo de aplicação para aplicação Web em Java.

    ![Preencher um nome, escolher aplicação Web em Java e clicar em criar](./media/app-insights-java-get-started/02-create.png)
3. Localize a chave de instrumentação do novo recurso. Terá de colar esta chave no seu projeto de código em breve.

    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/app-insights-java-get-started/03-key.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Adicionar o Application Insights SDK para Java ao projeto
*Escolha a forma adequada para o seu projeto.*

#### <a name="if-youre-using-eclipse-to-create-a-maven-or-dynamic-web-project-"></a>Se estiver a utilizar Eclipse para criar um projeto Maven ou Web Dinâmico...
Utilize o [Application Insights SDK para o Plug-in Java][eclipse].

#### <a name="if-youre-using-maven"></a>Se estiver a utilizar Maven...
Se o seu projeto já estiver configurado para utilizar Maven para a compilação, intercale o código seguinte no ficheiro pom.xml.

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[1.0,)</version>
      </dependency>
    </dependencies>
```

* *Fazer a compilação ou a soma de verificação dos erros de validação?* Tente utilizar uma versão específica, como: `<version>1.0.n</version>`. Pode encontrar a versão mais recente nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) ou no nosso [artefactos Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Tem de atualizar para um novo SDK?* Atualize as dependências do seu projeto.

#### <a name="if-youre-using-gradle"></a>Se estiver a utilizar Gradle...
Se o seu projeto já estiver configurado para utilizar Gradle para a compilação, intercale o código seguinte no ficheiro build.gradle.

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

```JSON

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }
```

* *Fazer a compilação ou a soma de verificação dos erros de validação? Tente utilizar uma versão específica, como:*`version:'1.0.n'`. *Pode encontrar a versão mais recente nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).*
* *Para atualizar para um novo SDK*
  * Atualize as dependências do seu projeto.

#### <a name="otherwise-"></a>Caso contrário...
Adicione manualmente o SDK:

1. Transfira o [Application Insights SDK para Java](https://aka.ms/aijavasdk).
2. Extraia os binários do ficheiro zip e adicione-os ao seu projeto.

### <a name="questions"></a>Perguntas...
* *Qual é a relação entre os componentes `-core` e `-web` no zip?*

  * `applicationinsights-core` dá-lhe a versão simples da API. Precisa sempre deste componente.
  * `applicationinsights-web` fornece métricas que controlam as contagens de pedidos HTTP e os tempos de resposta. Pode omitir este componente, se não pretender que esta telemetria seja recolhida automaticamente. Por exemplo, se pretender escrever a sua.
* *Para atualizar o SDK quando está a publicar alterações*

  * Transfira a versão mais recente de [Application Insights SDK para Java](https://aka.ms/qqkaq6) e substitua as anteriores.
  * As alterações são descritas nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-application-insights-xml-file"></a>3. Adicionar um ficheiro. xml do Application Insights
Adicione ApplicationInsights.xml à pasta de recursos do projeto ou certifique-se de que é adicionado ao caminho da classe de implementação do projeto. Copie o seguinte XML para a mesma.

Substitua a chave de instrumentação que recebeu do portal do Azure.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```


* A chave de instrumentação é enviada juntamente com todos os itens de telemetria e diz ao Application Insights para apresentá-la no seu recurso.
* O componente de Pedido HTTP é opcional. Envia automaticamente telemetria sobre pedidos e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de pedido HTTP. Atribui um identificador a cada pedido recebido pelo servidor e adiciona este identificador como uma propriedade a todos os itens de telemetria como a propriedade "Operation.Id". Permite-lhe correlacionar a telemetria associada a cada pedido, definindo um filtro em [pesquisa de diagnóstico][diagnostic].
* A chave do Application Insights pode ser transmitida dinamicamente a partir do portal do Azure como uma propriedade de sistema (-DAPPLICATION_INSIGHTS_IKEY=your_ikey). Se não houver uma propriedade definida, verifica a existência de uma variável de ambiente (APPLICATION_INSIGHTS_IKEY) nas Definições de Aplicações do Azure. Se ambas as propriedades não estiverem definidas, a InstrumentationKey predefinida é utilizada a partir do ApplicationInsights.xml. Esta sequência ajuda-o a gerir diferentes InstrumentationKeys para ambientes diferentes de forma dinâmica.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Maneiras alternativas de definir a chave de instrumentação
O SDK do Application Insights procura a chave pela seguinte ordem:

1. Propriedade de sistema: -DAPPLICATION_INSIGHTS_IKEY=your_ikey
2. Variável de ambiente: APPLICATION_INSIGHTS_IKEY
3. Ficheiro de configuração: ApplicationInsights.xml

Também pode [defini-lo no código](app-insights-api-custom-events-metrics.md#ikey):

```Java

    telemetryClient.InstrumentationKey = "...";
```

## <a name="4-add-an-http-filter"></a>4. Adicionar um filtro HTTP
O último passo da configuração permite que o componente de pedido HTTP registe cada pedido Web. (Não é necessário se pretender apenas a versão simples da API.)

Localize e abra o ficheiro web.xml no seu projeto e intercale o seguinte código no nó da aplicação Web, onde os filtros da aplicação estão configurados.

Para obter resultados mais exatos, o filtro deve ser mapeado antes de todos os outros filtros.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Se estiver a utilizar o Spring Web MVC 3.1 ou posterior
Edite estes elementos em *-servlet.xml para incluir o pacote do Application Insights:

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Se estiver a utilizar Struts 2
Adicione este item ao ficheiro de configuração de Struts (normalmente designado struts.xml ou struts-default.xml):

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

(Se tiver intercetores definidos numa pilha predefinida, o intercetor pode simplesmente ser adicionado a essa pilha.)

## <a name="5-run-your-application"></a>5. Executar a aplicação
Execute-a no modo de depuração no seu computador de desenvolvimento ou publique-a no seu servidor.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Ver a telemetria no Application Insights
Regresse ao seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Os dados de pedidos HTTP aparecem no painel de descrição geral. (Se não aparecerem, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![dados de exemplo](./media/app-insights-java-get-started/5-results.png)

[Saiba mais sobre métricas.][metrics]

Clique em qualquer gráfico para ver métricas agregadas mais detalhadas.

![](./media/app-insights-java-get-started/6-barchart.png)

> O Application Insights pressupõe que o formato de pedidos HTTP para aplicações MVC é: `VERB controller/action`. Por exemplo, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` e `GET Home/Product/sdf96vws` são agrupados em `GET Home/Product`. Este agrupamento permite agregações significativas de pedidos, como o número de pedidos e o tempo de execução médio dos pedidos.
>
>

### <a name="instance-data"></a>Dados de instâncias
Clique num tipo de pedido específico para ver instâncias individuais.

Dois tipos de dados são apresentados no Application Insights: dados agregados, armazenados e apresentados como médias, contagens e somas; e dados de instâncias - relatórios individuais de pedidos HTTP, exceções, vistas de página ou eventos personalizados.

Ao visualizar as propriedades de um pedido, pode ver os eventos de telemetria associados, como os pedidos e as exceções.

![](./media/app-insights-java-get-started/7-instance.png)

### <a name="analytics-powerful-query-language"></a>Análise: linguagem de consulta poderosa
À medida que se acumulam mais dados, pode executar consultas para agregar dados e localizar instâncias individuais.  A [análise](app-insights-analytics.md) é uma ferramenta poderosa para compreender o desempenho e a utilização, e para fins de diagnóstico.

![Exemplo de Análise](./media/app-insights-java-get-started/025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Instalar a aplicação no servidor
Agora publique a aplicação no servidor, permita que as pessoas a utilizem e veja a telemetria a ser mostrada no portal.

* Certifique-se que a firewall permite à aplicação enviar telemetria para estas portas:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se o tráfego de saída tiver de ser encaminhado através de uma firewall, defina as propriedades do sistema `http.proxyHost` e `http.proxyPort`.

* Nos servidores do Windows, instale:

  * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Este componente ativa os contadores de desempenho.)


## <a name="exceptions-and-request-failures"></a>Exceções e falhas de pedido
As exceções não processadas são recolhidas automaticamente:

![Abrir Definições, Falhas](./media/app-insights-java-get-started/21-exceptions.png)

Para recolher dados de outras exceções, tem duas opções:

* [Inserir chamadas para trackException() no seu código][apiexceptions].
* [Instalar o Agente Java no servidor](app-insights-java-agent.md). Especifique os métodos que pretende ver.

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorizar chamadas de método e dependências externas
[Instale o Agente Java](app-insights-java-agent.md) para registar métodos internos especificados e as chamadas efetuadas através de JDBC, com dados de temporização.

## <a name="performance-counters"></a>Contadores de desempenho
Abra **Definições**, **Servidores** para ver uma gama de contadores de desempenho.

![](./media/app-insights-java-get-started/11-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar a recolha do contador de desempenho
Para desativar a recolha do conjunto padrão de contadores de desempenho, adicione o seguinte código ao nó de raiz do ficheiro ApplicationInsights.xml:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Recolher contadores de desempenho adicionais
Pode especificar contadores de desempenho adicionais para recolha.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Contadores JMX (expostos pela Máquina Virtual Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – O nome apresentado no portal do Application Insights.
* `objectName` – O nome do objeto JMX.
* `attribute` – O atributo do nome do objeto JMX a obter
* `type` (opcional) - O tipo de atributo do objeto JMX:
  * Predefinição: um tipo simples, como int ou long.
  * `composite`: os dados de contador de desempenho estão no formato “Attribute.Data”
  * `tabular`: os dados de contador de desempenho estão no formato de uma linha de tabela

#### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows
Cada [contador de desempenho do Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) é membro de uma categoria (da mesma forma que um campo é membro de uma classe). As categorias podem ser globais ou podem ter instâncias numeradas ou nomeadas.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – o nome apresentado no portal do Application Insights.
* categoryName – a categoria do contador de desempenho (objeto de desempenho) à qual este contador de desempenho está associado.
* counterName – o nome do contador de desempenho.
* instanceName – o nome da instância da categoria do contador de desempenho ou uma cadeia vazia (""), se a categoria contiver uma única instância. Se o categoryName for Processo e o contador de desempenho que pretende recolher é do processo JVM atual no qual a aplicação está em execução, especifique `"__SELF__"`.

Os contadores de desempenho ficam visíveis como métricas personalizadas no [Explorador de Métricas][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Contadores de desempenho Unix
* [Instale collectd com o plug-in do Application Insights](app-insights-java-collectd.md) para obter uma ampla variedade de dados de sistema e de rede.

## <a name="get-user-and-session-data"></a>Obter dados de utilizador e de sessão
OK, está a enviar telemetria a partir do seu servidor Web. Para obter a vista completa em 360 graus da sua aplicação, agora pode adicionar mais monitorização:

* [Adicione a telemetria às suas páginas Web][usage] para monitorizar vistas de página e métricas de utilizador.
* [Configure testes Web][availability] para certificar-se de que a aplicação permanece em direto e reativa.

## <a name="capture-log-traces"></a>Capturar rastreios de registo
Pode utilizar o Application Insights para examinar registos de Log4J, Logback ou outras arquiteturas de registo. Pode correlacionar os registos com pedidos HTTP e outra telemetria. [Saiba como][javalogs].

## <a name="send-your-own-telemetry"></a>Enviar a sua própria telemetria
Agora que instalou o SDK, pode utilizar a API para enviar a sua própria telemetria.

* [Controle eventos personalizados e métricas][api] para saber o que os utilizadores estão a fazer com a sua aplicação.
* [Pesquise eventos e registos][diagnostic] para ajudar a diagnosticar problemas.

## <a name="availability-web-tests"></a>Testes Web de disponibilidade
O Application Insights pode testar o seu site em intervalos regulares para verificar se está a funcionar e a responder bem. [Para configurar][availability], clique em testes Web.

![Clique em Testes Web e, em seguida, em Adicionar teste Web](./media/app-insights-java-get-started/31-config-web-test.png)

Irá obter gráficos de tempos de resposta e notificações por e-mail, se o seu site ficar inativo.

![Exemplo de teste Web](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Saiba mais sobre testes Web de disponibilidade.][availability]

## <a name="questions-problems"></a>Tem dúvidas? Problemas?
[Resolução de problemas de Java](app-insights-java-troubleshoot.md)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes
* [Chamadas de dependência do monitor](app-insights-java-agent.md)
* [Contadores de desempenho Unix do monitor](app-insights-java-collectd.md)
* Adicione [monitorização das suas páginas Web](app-insights-javascript.md), para monitorizar os tempos de carregamento da página, as chamadas AJAX e as exceções de browser.
* Escreva [telemetria personalizada](app-insights-api-custom-events-metrics.md) para controlar a utilização no browser ou no servidor.
* Crie [dashboards](app-insights-dashboards.md) para reunir os gráficos principais para monitorização do seu sistema.
* Utilize o [Analytics](app-insights-analytics.md) para consultas eficientes através de telemetria a partir da aplicação
* Para obter mais informações, visite [Azure para programadores Java](/java/azure).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#trackexception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-javascript.md
