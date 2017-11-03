---
title: "Monitorizar o desempenho de aplicação web Java no Linux - Azure | Microsoft Docs"
description: "Expandido a monitorização da aplicação do seu site em Java com CollectD Plug-in para o Application Insights."
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: cde0fc020f1774e0e7669e7573e4aaff3534b34c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: Linux métricas de desempenho no Application Insights


Para explorar métricas de desempenho do sistema de Linux no [Application Insights](app-insights-overview.md), instalar [collectd](http://collectd.org/), em conjunto com o plug-in Application Insights. Esta solução de open source reúne diversas estatísticas de rede e do sistema.

Normalmente, utilizará collectd se já tiver [instrumentados o seu serviço web de Java com o Application Insights][java]. Proporciona mais dados para ajudar a melhorar o desempenho da aplicação ou diagnosticar problemas. 

![gráficos de exemplo](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>Obter a chave de instrumentação
No [portal do Microsoft Azure](https://portal.azure.com), abra o [Application Insights](app-insights-overview.md) recurso onde pretende que os dados sejam apresentados. (Ou [criar um novo recurso](app-insights-create-new-resource.md).)

Efetuar uma cópia da chave de instrumentação que identifica o recurso.

![Procurar tudo, abra o seu recurso e, em seguida, no Essentials lista pendente, selecione e copie a chave de instrumentação](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Instale collectd e o plug-in
As máquinas de servidor Linux:

1. Instalar [collectd](http://collectd.org/) versão 5.4.0 ou posterior.
2. Transferir o [Plug-in do Application Insights collectd escritor](https://aka.ms/aijavasdk). Tenha em atenção o número de versão.
3. Copiar o plug-in JAR no `/usr/share/collectd/java`.
4. Editar `/etc/collectd/collectd.conf`:
   * Certifique-se de que [o plug-in de Java](https://collectd.org/wiki/index.php/Plugin:Java) está ativada.
   * Atualize o JVMArg para o java.class.path para incluir o seguinte JAR. Atualize o número de versão para corresponder o um transferiu:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adicione este fragmento, utilize a chave de instrumentação do seu recurso:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Segue-se parte de um ficheiro de configuração de exemplo:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Configurar outros [plug-ins de collectd](https://collectd.org/wiki/index.php/Table_of_Plugins), que pode recolher dados de várias origens diferentes.

Reiniciar collectd de acordo com a respetiva [manual](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Ver os dados no Application Insights
No recurso do Application Insights, abra [Explorador de métricas e adicione gráficos][metrics], selecionando as métricas que pretende ver a categoria personalizada.

![](./media/app-insights-java-collectd/result.png)

Por predefinição, as métricas são agregadas em todas as máquinas anfitrião a partir da qual as métricas foram recolhidas. Para ver as métricas por anfitrião, no painel de detalhes do gráfico, ative agrupamento e, em seguida, escolha Agrupar por CollectD anfitrião.

## <a name="to-exclude-upload-of-specific-statistics"></a>Para excluir o carregamento das estatísticas específicos
Por predefinição, o plug-in do Application Insights envia todos os dados recolhidos por todos os o collectd enabled 'read' Plug-ins. 

Para excluir os dados da específicas plug-ins ou origens de dados:

* Edite o ficheiro de configuração. 
* No `<Plugin ApplicationInsightsWriter>`, adicione as linhas directiva como esta:

| Diretiva | Efeito |
| --- | --- |
| `Exclude disk` |Excluir todos os dados recolhidos pelo `disk` Plug-in |
| `Exclude disk:read,write` |Excluir origens com o nome `read` e `write` do `disk` Plug-in. |

Diretivas separadas com uma nova linha.

## <a name="problems"></a>Problemas?
*Não consigo ver dados no portal*

* Abra [pesquisa] [ diagnostic] para ver se chegou os eventos não processados. Por vezes, demorar mais tempo a aparecer no Explorador de métricas.
* Poderá ter de [definir exceções de firewall para dados de saída](app-insights-ip-addresses.md)
* Ative o rastreio no plug-in do Application Insights. Adicione esta linha dentro `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Abra um terminal e iniciar collectd em modo verboso, para ver os problemas que está a comunicar:
  * `sudo collectd -f`

## <a name="known-issue"></a>Problema conhecido

O plug-in Application Insights escrever é incompatível com determinadas plug-ins de leitura. Alguns plug-ins, por vezes, enviam "NaN" onde o plug-in do Application Insights espera um número de vírgula flutuante.

Sintoma: O registo de collectd mostra erros que incluem "AI:... SyntaxError: token inesperado N ".

Solução: Excluir os dados recolhidos pelo plug-ins de escrita do problema. 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md


