---
title: "Introdução ao Azure Application Insights com o Java no Eclipse | Documentos da Microsoft"
description: "Utilizar o plug-in do Eclipse para adicionar o desempenho e a monitorização de utilização para o seu site em Java com o Application Insights"
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: e88c9f53-cd90-4abc-b097-1f170937908e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: mbullwin
ms.openlocfilehash: 616cbfed405454d2abbb6bb526166d2c72e4365d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="get-started-with-application-insights-with-java-in-eclipse"></a>Introdução ao Application Insights com o Java no Eclipse
O Application Insights SDK envia a telemetria da sua aplicação web de Java para que possa analisar utilização e desempenho. O Eclipse Plug-in para o Application Insights instala automaticamente o SDK no projeto para que a tirar partido da telemetria de caixa, além de uma API que pode utilizar para escrever telemetria personalizada.   

## <a name="prerequisites"></a>Pré-requisitos
Atualmente o funciona Plug-in para projetos do Maven e projetos Web dinâmico no Eclipse.
([Adicionar o Application Insights para outros tipos de projeto Java][java].)

Precisa de:

* Oracle JRE 1.6 ou posterior
* Uma subscrição do [Microsoft Azure](https://azure.microsoft.com/).
* [IDE Eclipse para programadores de Java EE](http://www.eclipse.org/downloads/), Indigo ou posterior.
* Windows 7 ou posterior, ou Windows Server 2008 ou posterior

## <a name="install-the-sdk-on-eclipse-one-time"></a>Instalar o SDK no Eclipse (uma vez)
Só tem de executar este uma vez por máquina. Este passo instala um conjunto de ferramentas que, em seguida, pode adicionar o SDK para cada Dynamic Web Project.

1. No Eclipse, clique em Ajuda, instalar o novo Software.

    ![Ajudar, instale o novo Software](./media/app-insights-java-eclipse/0-plugin.png)
2. O SDK está a ser http://dl.microsoft.com/eclipse, sob o Toolkit do Azure.
3. Desmarque **contacte todos os sites de atualização...**

    ![Para o Application Insights SDK, desmarque contacte todos os sites de atualização](./media/app-insights-java-eclipse/1-plugin.png)

Siga os passos restantes para cada projeto de Java.

## <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso do Application Insights no Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Crie um novo recurso do Application Insights. Defina o tipo de aplicação para aplicação Web em Java.  

    ![Click + e escolha Application Insights](./media/app-insights-java-eclipse/01-create.png)  

4. Localize a chave de instrumentação do novo recurso. Terá de colar isto no seu projeto de código em breve.  

    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/app-insights-java-eclipse/03-key.png)  

## <a name="add-application-insights-to-your-project"></a>Adicionar as Informações da Aplicação ao seu projeto
1. Adicione o Application Insights no menu de contexto do seu projeto de web de Java.

    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/app-insights-java-eclipse/02-context-menu.png)
2. Cole a chave de instrumentação que recebeu do portal do Azure.

    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/app-insights-java-eclipse/03-ikey.png)

A chave é enviada juntamente com todos os itens de telemetria e diz ao Application Insights para apresentá-la no seu recurso.

## <a name="run-the-application-and-see-metrics"></a>Executar a aplicação e ver as métricas
Execute a sua aplicação.

Regresse ao seu recurso do Application Insights no Microsoft Azure.

Os dados de pedidos HTTP irão aparecer no painel de descrição geral. (Se não aparecerem, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![Resposta do servidor, contagens de pedidos e falhas ](./media/app-insights-java-eclipse/5-results.png)

Clique em qualquer gráfico para ver métricas mais detalhadas.

![Contagens de pedidos por nome](./media/app-insights-java-eclipse/6-barchart.png)

[Saiba mais sobre métricas.][metrics]

E, ao visualizar as propriedades de um pedido, pode ver os eventos de telemetria associados, como os pedidos e exceções.

![Todos os rastreios para este pedido](./media/app-insights-java-eclipse/7-instance.png)

## <a name="client-side-telemetry"></a>Telemetria do lado do cliente
No painel início rápido, clique em obter código para monitorizar as minhas páginas web:

![No painel de descrição geral da aplicação, escolha Início Rápido, Obter código para monitorizar as minhas páginas Web. Copie o script.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Inserir o fragmento de código no cabeçalho dos ficheiros HTML.

#### <a name="view-client-side-data"></a>Visualizar os dados do lado do cliente
Abrir páginas web atualizado e utilizá-los. Aguarde um minuto ou dois, em seguida, regressar ao Application Insights e abra o painel de utilização. (A partir do painel de descrição geral, desloque para baixo e clique em utilização.)

As métricas, utilizador, sessões e visualizações de página serão apresentada no painel de utilização:

![Sessões, utilizadores e vistas de página](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[Saiba mais sobre como configurar a telemetria do lado do cliente.][usage]

## <a name="publish-your-application"></a>Publicar a aplicação
Agora publique a aplicação no servidor, permita que as pessoas a utilizem e veja a telemetria a ser mostrada no portal.

* Certifique-se que a firewall permite à aplicação enviar telemetria para estas portas:

  * dc.services.visualstudio.com:443
  * dc.services.visualstudio.com:80
  * f5.services.visualstudio.com:443
  * f5.services.visualstudio.com:80
* Nos servidores do Windows, instale:

  * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Isto ativa os contadores de desempenho.)

## <a name="exceptions-and-request-failures"></a>Exceções e falhas de pedido
As exceções não processadas são recolhidas automaticamente:

![](./media/app-insights-java-eclipse/21-exceptions.png)

Para recolher dados de outras exceções, tem duas opções:

* [Inserir chamadas para TrackException no seu código](app-insights-api-custom-events-metrics.md#trackexception).
* [Instalar o Agente Java no servidor](app-insights-java-agent.md). Especifique os métodos que pretende ver.

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorizar chamadas de método e dependências externas
[Instale o Agente Java](app-insights-java-agent.md) para registar métodos internos especificados e as chamadas efetuadas através de JDBC, com dados de temporização.

## <a name="performance-counters"></a>Contadores de desempenho
No painel da descrição geral, desloque para baixo e clique em de **servidores** mosaico. Verá um intervalo de contadores de desempenho.

![Desloque para baixo para clique em que servidores de mosaico](./media/app-insights-java-eclipse/11-perf-counters.png)

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

![](./media/app-insights-java-eclipse/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Contadores de desempenho Unix
* [Instale collectd com o plug-in do Application Insights](app-insights-java-collectd.md) para obter uma ampla variedade de dados de sistema e de rede.

## <a name="availability-web-tests"></a>Testes Web de disponibilidade
O Application Insights pode testar o seu site em intervalos regulares para verificar se está a funcionar e a responder bem. [Para configurar][availability], desloque para baixo para clicar em disponibilidade.

![Desloque para baixo, clique em Disponibilidade e, em seguida, em Adicionar teste Web](./media/app-insights-java-eclipse/31-config-web-test.png)

Irá obter gráficos de tempos de resposta e notificações por e-mail, se o seu site ficar inativo.

![Exemplo de teste Web](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[Saiba mais sobre testes Web de disponibilidade.][availability]

## <a name="diagnostic-logs"></a>Registos de diagnósticos
Se estiver a utilizar Logback ou Log4J (v1.2 ou v 2.0) para o rastreio, pode fazer com que os registos de rastreio automaticamente enviados para o Application Insights, onde pode explorar e procurar nos mesmos.

[Saiba mais sobre os registos de diagnóstico][javalogs]

## <a name="custom-telemetry"></a>Telemetria personalizada
Insira alguns linhas de código na aplicação web Java para saber que os utilizadores estão a fazer com o mesmo ou para ajudar a diagnosticar problemas.

Pode inserir código tanto na página web JavaScript do lado do servidor de Java.

[Saiba mais sobre a telemetria personalizada][track]

## <a name="next-steps"></a>Passos seguintes
#### <a name="detect-and-diagnose-issues"></a>Detetar e diagnosticar problemas
* [Adicionar telemetria de cliente web] [ usage] para obter telemetria de desempenho do cliente web.
* [Configure testes Web][availability] para certificar-se de que a aplicação permanece em direto e reativa.
* [Pesquise eventos e registos][diagnostic] para ajudar a diagnosticar problemas.
* [Capturar rastreios de Log4J ou Logback][javalogs]

#### <a name="track-usage"></a>Controlar a utilização
* [Adicionar telemetria de cliente web] [ usage] para monitorizar vistas de página e métricas de utilizador básico.
* [Controlar métricas e eventos personalizados](app-insights-web-track-usage.md) para saber mais sobre como a aplicação for utilizada, tanto o cliente e o servidor.

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md
