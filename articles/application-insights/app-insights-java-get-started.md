<properties
    pageTitle="Análise de aplicação Web em Java com o Application Insights | Microsoft Azure"
    description="Monitorize o desempenho e a utilização do seu site em Java com o Application Insights. "
    services="application-insights"
    documentationCenter="java"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/12/2016"
    ms.author="awills"/>

# Introdução ao Application Insights num projeto Web em Java

*O Application Insights está em Pré-Visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

O [Application Insights](https://azure.microsoft.com/services/application-insights/) é um serviço de análise extensível que o ajuda a compreender o desempenho e a utilização da aplicação em direto. Utilize-o para [detetar e a diagnosticar problemas de desempenho e exceções](app-insights-detect-triage-diagnose.md), e [escrever código][api] para controlar o que os utilizadores fazem com a sua aplicação.

![dados de exemplo](./media/app-insights-java-get-started/5-results.png)

O Application Insights suporta aplicações em Java em execução no Linux, Unix ou Windows.

Precisa de:

* Oracle JRE 1.6 ou posterior, ou Zulu JRE 1.6 ou posterior
* Uma subscrição do [Microsoft Azure](https://azure.microsoft.com/). (Poderá começar pela [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).)

*Se tiver uma aplicação Web que já está em direto, pode seguir o procedimento alternativo para [adicionar o SDK no tempo de execução ao servidor Web](app-insights-java-live.md). Essa alternativa evita a reconstrução do código, mas não tem a opção para escrever código de modo a controlar a atividade do utilizador.*


## 1. Obter uma chave de instrumentação do Application Insights

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).
2. Crie um novo recurso do Application Insights.

    ![Click + e escolha Application Insights](./media/app-insights-java-get-started/01-create.png)
3. Defina o tipo de aplicação para aplicação Web em Java.

    ![Preencher um nome, escolher aplicação Web em Java e clicar em criar](./media/app-insights-java-get-started/02-create.png)
4. Localize a chave de instrumentação do novo recurso. Terá de colar isto no seu projeto de código em breve.

    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/app-insights-java-get-started/03-key.png)

## 2. Adicionar o Application Insights SDK para Java ao projeto

*Escolha a forma adequada para o seu projeto.*

#### Se estiver a utilizar Eclipse para criar um projeto Maven ou Web Dinâmico...

Utilize o [Application Insights SDK para o Plug-in Java][eclipse].

#### Se estiver a utilizar Maven...

Se o seu projeto já estiver configurado para utilizar Maven para a compilação, intercale o código seguinte no ficheiro pom.xml.

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

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


* *Fazer a compilação ou a soma de verificação dos erros de validação?* Tente utilizar uma versão específica, como: `<version>1.0.n</version>`. Pode encontrar a versão mais recente nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) ou no nosso [artefactos Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Tem de atualizar para um novo SDK?* Atualize as dependências do seu projeto.

#### Se estiver a utilizar Gradle...

Se o seu projeto já estiver configurado para utilizar Gradle para a compilação, intercale o código seguinte no ficheiro build.gradle.

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *Fazer a compilação ou a soma de verificação dos erros de validação? Tente utilizar uma versão específica, como:*`version:'1.0.n'`. *Pode encontrar a versão mais recente nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).*
* *Para atualizar para um novo SDK*
 * Atualize as dependências do seu projeto.

#### Caso contrário...

Adicione manualmente o SDK:

1. Transfira o [Application Insights SDK para Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html).
2. Extraia os binários do ficheiro zip e adicione-os ao seu projeto.

### Perguntas...

* *Qual é a relação entre os componentes `-core` e `-web` no zip?*

 * `applicationinsights-core` dá-lhe a versão simples da API. Precisa sempre dela.
 * `applicationinsights-web` fornece métricas que controlam as contagens de pedidos HTTP e os tempos de resposta. Pode omitir isto, se não pretender que esta telemetria seja recolhida automaticamente. Por exemplo, se pretender escrever a sua.

* *Para atualizar o SDK quando está a publicar alterações*
 * Transfira a versão mais recente de [Application Insights SDK para Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.zip) e substitua as anteriores.
 * As alterações são descritas nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).



## 3. Adicionar um ficheiro. XML do Application Insights

Adicione ApplicationInsights.xml à pasta de recursos do projeto ou certifique-se de que é adicionado ao caminho da classe de implementação do projeto. Copie o seguinte XML para a mesma.

Substitua a chave de instrumentação que recebeu do portal do Azure.

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
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* A chave de instrumentação é enviada juntamente com todos os itens de telemetria e diz ao Application Insights para apresentá-la no seu recurso.
* O componente de Pedido HTTP é opcional. Envia automaticamente telemetria sobre pedidos e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de pedido HTTP. Atribui um identificador a cada pedido recebido pelo servidor e adiciona-o como uma propriedade a todos os itens de telemetria como a propriedade “Operation.Id”. Permite-lhe correlacionar a telemetria associada a cada pedido, definindo um filtro em [pesquisa de diagnóstico][diagnóstico].

## 4. Adicionar um filtro HTTP

O último passo da configuração permite que o componente de pedido HTTP registe cada pedido Web. (Não é necessário se pretender apenas a versão simples da API.)

Localize e abra o ficheiro web.xml no seu projeto e intercale o seguinte código no nó da aplicação Web, onde os filtros da aplicação estão configurados.

Para obter resultados mais exatos, o filtro deve ser mapeado antes de todos os outros filtros.

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

#### Se estiver a utilizar MVC 3.1 ou posterior

Edite estes elementos para incluir o pacote do Application Insights:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Se estiver a utilizar Struts 2

Adicione este item ao ficheiro de configuração de Struts (normalmente designado struts.xml ou struts-default.xml):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(Se tiver intercetores definidos numa pilha predefinida, o intercetor pode simplesmente ser adicionado a essa pilha.)



## 5. Executar a aplicação

Execute-a no modo de depuração no seu computador de desenvolvimento ou publique-a no seu servidor.

## 6. Ver a telemetria no Application Insights


Regresse ao seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Os dados de pedidos HTTP irão aparecer no painel de descrição geral. (Se não aparecerem, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![dados de exemplo](./media/app-insights-java-get-started/5-results.png)

[Saiba mais sobre as métricas.][métricas]

Clique em qualquer gráfico para ver métricas agregadas mais detalhadas.

![](./media/app-insights-java-get-started/6-barchart.png)

> O Application Insights pressupõe que o formato de pedidos HTTP para aplicações MVC é: `VERB controller/action`. Por exemplo, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` e `GET Home/Product/sdf96vws` irão ser agrupados em `GET Home/Product`. Isto permite agregações significativas de pedidos, como o número de pedidos e o tempo de execução médio dos pedidos.


### Dados de instâncias 

Clique num tipo de pedido específico para ver instâncias individuais. 

Dois tipos de dados são apresentados no Application Insights: dados agregados, armazenados e apresentados como médias, contagens e somas; e dados de instâncias - relatórios individuais de pedidos HTTP, exceções, vistas de página ou eventos personalizados.

Ao visualizar as propriedades de um pedido, pode ver os eventos de telemetria associados, como os pedidos e as exceções.

![](./media/app-insights-java-get-started/7-instance.png)


### Análise: linguagem de consulta poderosa

À medida que se acumulam mais dados, pode executar consultas para agregar dados e localizar instâncias individuais. A [análise]() é uma ferramenta poderosa para compreender o desempenho e a utilização, e para fins de diagnóstico.

![Exemplo de Análise](./media/app-insights-java-get-started/025.png)


## 5. Instalar a aplicação no servidor

Agora publique a aplicação no servidor, permita que as pessoas a utilizem e veja a telemetria a ser mostrada no portal.

* Certifique-se que a firewall permite à aplicação enviar telemetria para estas portas:

 * dc.services.visualstudio.com:443
 * dc.services.visualstudio.com:80
 * f5.services.visualstudio.com:443
 * f5.services.visualstudio.com:80


* Nos servidores do Windows, instale:

 * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Isto ativa os contadores de desempenho.)

## Exceções e falhas de pedido

As exceções não processadas são recolhidas automaticamente:

![Desloque para baixo e clique no mosaico Falhas](./media/app-insights-java-get-started/21-exceptions.png)

Para recolher dados de outras exceções, tem duas opções:

* [Inserir chamadas para trackException() no seu código][apiexceptions]. 
* [Instalar o Agente Java no servidor](app-insights-java-agent.md). Especifique os métodos que pretende ver.


## Monitorizar chamadas de método e dependências externas

[Instale o Agente Java](app-insights-java-agent.md) para registar métodos internos especificados e as chamadas efetuadas através de JDBC, com dados de temporização.


## Contadores de desempenho

Clique no mosaico **Servidores** e verá um intervalo de contadores de desempenho.


![](./media/app-insights-java-get-started/11-perf-counters.png)

### Personalizar a recolha do contador de desempenho

Para desativar a recolha do conjunto padrão de contadores de desempenho, adicione o seguinte código ao nó de raiz do ficheiro ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Recolher contadores de desempenho adicionais

Pode especificar contadores de desempenho adicionais para recolha.

#### Contadores JMX (expostos pela Máquina Virtual Java)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*   `displayName` – O nome apresentado no portal do Application Insights.
*   `objectName` – O nome do objeto JMX.
*   `attribute` – O atributo do nome do objeto JMX a obter
*   `type` (opcional) - O tipo de atributo do objeto JMX:
 *  Predefinição: um tipo simples, como int ou long.
 *  `composite`: os dados de contador de desempenho estão no formato “Attribute.Data”
 *  `tabular`: os dados de contador de desempenho estão no formato de uma linha de tabela



#### Contadores de desempenho do Windows

Cada [contador de desempenho do Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) é membro de uma categoria (da mesma forma que um campo é membro de uma classe). As categorias podem ser globais ou podem ter instâncias numeradas ou nomeadas.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*   displayName – o nome apresentado no portal do Application Insights.
*   categoryName – a categoria do contador de desempenho (objeto de desempenho) à qual este contador de desempenho está associado.
*   counterName – o nome do contador de desempenho.
*   instanceName – o nome da instância da categoria do contador de desempenho ou uma cadeia vazia (""), se a categoria contiver uma única instância. Se o categoryName for Processo e o contador de desempenho que pretende recolher é do processo JVM atual no qual a aplicação está em execução, especifique `"__SELF__"`.

Os contadores de desempenho ficam visíveis como métricas personalizadas no [Explorador de Métricas][métricas].

![](./media/app-insights-java-get-started/12-custom-perfs.png)


### Contadores de desempenho Unix

* [Instale collectd com o plug-in do Application Insights](app-insights-java-collectd.md) para obter uma ampla variedade de dados de sistema e de rede.

## Obter dados de utilizador e de sessão

OK, está a enviar telemetria a partir do seu servidor Web. Para obter a vista completa em 360 graus da sua aplicação, agora pode adicionar mais monitorização:

* [Adicione a telemetria às suas páginas Web][utilização] para monitorizar vistas de página e métricas de utilizador.
* [Configure testes Web][disponibilidade] para certificar-se de que a aplicação permanece em direto e reativa.

## Capturar rastreios de registo

Pode utilizar o Application Insights para examinar registos de Log4J, Logback ou outras arquiteturas de registo. Pode correlacionar os registos com pedidos HTTP e outra telemetria. [Saiba como][javalogs].

## Enviar a sua própria telemetria

Agora que instalou o SDK, pode utilizar a API para enviar a sua própria telemetria.

* [Controle eventos personalizados e métricas][api] para saber o que os utilizadores estão a fazer com a sua aplicação.
* [Pesquise eventos e registos][diagnóstico] para ajudar a diagnosticar problemas.


## Testes Web de disponibilidade

O Application Insights pode testar o seu site em intervalos regulares para verificar se está a funcionar e a responder bem. [Para configurar][disponibilidade], desloque para baixo para clicar em Disponibilidade.

![Desloque para baixo, clique em Disponibilidade e, em seguida, em Adicionar teste Web](./media/app-insights-java-get-started/31-config-web-test.png)

Irá obter gráficos de tempos de resposta e notificações por e-mail, se o seu site ficar inativo.

![Exemplo de teste Web](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Saiba mais sobre testes Web de disponibilidade.][disponibilidade] 






## Tem dúvidas? Problemas?

[Resolução de problemas de Java](app-insights-java-troubleshoot.md)

## Passos seguintes

Para obter mais informações, veja [Centro para Programadores do Java](/develop/java/).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[disponibilidade]: app-insights-monitor-web-app-availability.md
[diagnóstico]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[métricas]: app-insights-metrics-explorer.md
[utilização]: app-insights-web-track-usage.md



<!--HONumber=Jun16_HO2-->


