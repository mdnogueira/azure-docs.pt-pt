---
title: Resolver problemas relacionados com o Application Insights num projeto web Java
description: "Guia de resolução de problemas - monitorização de aplicações de Java em direto com o Application Insights."
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: 6b1cfa2b52e8e9e2b6a8ab87be6d4269cbe3f1cf
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Resolução de problemas e Perguntas e Respostas para o Application Insights para Java
Questões ou problemas com [Azure Application Insights em Java][java]? Eis algumas sugestões.

## <a name="build-errors"></a>Erros de compilação
**No Eclipse, ao adicionar o Application Insights SDK através do Maven ou Gradle, posso obter erros de validação de compilação ou a soma de verificação.**

* Se a dependência <version> elemento estiver a utilizar um padrão com carateres universais (por exemplo, (Maven) `<version>[1.0,)</version>` ou (Gradle) `version:'1.0.+'`), tente especificar uma versão específica em vez disso, como `1.0.2`. Consulte o [notas de versão](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) para a versão mais recente.

## <a name="no-data"></a>Sem dados
**Posso adicionar o Application Insights com êxito e foi executada a minha aplicação, mas posso nunca viu dados no portal.**

* Aguarde um pouco e clique em Atualizar. Os gráficos atualizam-se periodicamente, mas também pode atualizar manualmente. O intervalo de atualização depende o intervalo de tempo do gráfico.
* Verifique se tem uma chave de instrumentação definida no ficheiro ApplicationInsights.xml (na pasta de recursos no seu projeto)
* Certifique-se de que não existe nenhum `<DisableTelemetry>true</DisableTelemetry>` nó no ficheiro xml.
* Na sua firewall poderá ter de abrir as portas TCP 80 e 443 para tráfego de saída para dc.services.visualstudio.com. Consulte o [lista completa de exceções de firewall](app-insights-ip-addresses.md)
* No Microsoft Azure Iniciar quadro, observe o mapa de estado do serviço. Se existirem algumas indicações de alerta, aguarde até que tenham devolvido para OK e, em seguida, feche e volte a abrir o painel de aplicações do Application Insights.
* Ativar o registo para a janela de consola IDE, adicionando um `<SDKLogger />` elemento sob o nó de raiz no ficheiro ApplicationInsights.xml (na pasta de recursos no seu projeto) e verifique a existência de entradas precedido pela [erro].
* Certifique-se de que o ficheiro ApplicationInsights.xml correto tem foi carregado com êxito pelo Java SDK, observando mensagens de saída da consola para uma instrução "ficheiro de configuração foi encontrou".
* Se o ficheiro de configuração não for encontrado, consulte as mensagens de saída para ver onde o ficheiro de configuração está a ser procurado para e certifique-se de que o ApplicationInsights.xml está localizada dessas localizações de pesquisa. Como uma regra geral, é possível colocar o ficheiro de configuração quase a JARs do Application Insights SDK. Por exemplo: no Tomcat, isto seria significa que a pasta do WEB-INF/lib.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Posso utilizado para ver os dados, mas foi parado
* Verifique o [Estado blogue](http://blogs.msdn.com/b/applicationinsights-status/).
* Atingiu a quota mensal de pontos de dados? Abra as definições/Quota e preços para descobrir. Se Sim, pode atualizar o plano ou paga capacidade adicional. Consulte o [preços esquema](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que estou a esperar
* Abra as Quotas e preços painel e verifique se [amostragem](app-insights-sampling.md) estiver a ser utilizada. (transmissão de 100% significa que não se encontra na operação de amostragem.) O serviço do Application Insights pode ser definido para aceitar apenas uma fração de telemetria que são recebidos a partir da sua aplicação. Isto ajuda a manter dentro da sua quota mensal de telemetria. 

## <a name="no-usage-data"></a>Não existem dados de utilização
**Posso ver dados sobre pedidos e tempos de resposta, mas nenhuma vista de página, browser ou dados de utilizador.**

É configurada com êxito a aplicação para enviar telemetria a partir do servidor. Agora que o passo seguinte é [configurar as suas páginas web para enviar telemetria a partir do browser][usage].

Em alternativa, se o cliente é uma aplicação num [telefone ou outro dispositivo][platforms], pode enviar telemetria a partir daí. 

Utilize a mesma chave de instrumentação para configurar a telemetria de cliente e do servidor. Os dados serão apresentados no mesmo recurso do Application Insights e poderá correlacionar eventos de cliente e servidor.


## <a name="disabling-telemetry"></a>Desativar a telemetria
**Como desativar a coleção de telemetria?**

No código:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Ou** 

Atualize ApplicationInsights.xml (na pasta de recursos no seu projeto). Adicione o seguinte sob o nó de raiz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Utilizar o método XML, terá de reiniciar a aplicação quando altera o valor.

## <a name="changing-the-target"></a>Alterar o destino
**Como posso alterar a qual os meus projeto envia dados para o recurso do Azure?**

* [Obter a chave de instrumentação do novo recurso.][java]
* Se tiver adicionado o Application Insights ao seu projeto através do Toolkit do Azure para o Eclipse, clique com o botão direito do rato em projeto web, selecione **Azure**, **configurar o Application Insights**e altere a chave.
* Caso contrário, atualize a chave no ApplicationInsights.xml na pasta de recursos no seu projeto.

## <a name="debug-data-from-the-sdk"></a>Dados do SDK do Debug

**Como posso saber se que o SDK está a fazer?**

Para obter mais informações sobre o que acontece na API, adicione `<SDKLogger/>` sob o nó de raiz do ficheiro de configuração ApplicationInsights.xml.

Também pode instruir o registo para um ficheiro de saída:

```XML

    <SDKLogger type="FILE">
      <enabled>True</enabled>
      <UniquePrefix>JavaSDKLog</UniquePrefix>
    </SDKLogger>
```

Os ficheiros podem ser encontrados em `%temp%\javasdklogs` ou `java.io.tmpdir` em caso de servidor Tomcat.


## <a name="the-azure-start-screen"></a>No ecrã inicial do Azure
**Estou à procura no [portal do Azure](https://portal.azure.com). O mapa mais algo sobre a minha aplicação?**

Não, mostra o estado de funcionamento dos servidores do Azure em todo o mundo.

*Do painel de início do Azure (home ecrã), como encontrar dados sobre a minha aplicação?*

Partindo do princípio [configurar a sua aplicação para o Application Insights][java], clique em Procurar, selecione o Application Insights e selecione o recurso de aplicação que criou para a sua aplicação. Para obter existe mais rapidamente no futuro, pode afixar a aplicação para o painel de início.

## <a name="intranet-servers"></a>Servidores da intranet
**Pode monitorizar um servidor na minha intranet?**

Sim, especificar que o servidor pode enviar telemetria ao portal do Application Insights através da internet pública. 

Na sua firewall poderá ter de abrir as portas TCP 80 e 443 para tráfego de saída para dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Retenção de dados
**Quanto os dados são mantidos no portal? É seguro?**

Consulte [retenção de dados e privacidade][data].

## <a name="debug-logging"></a>O registo de depuração
Application Insights utiliza `org.apache.http`. Isto foi reposicionado no Application Insights core v7 sob o espaço de nomes `com.microsoft.applicationinsights.core.dependencies.http`. Isto permite que o Application Insights processar cenários onde diferentes versões do mesmo `org.apache.http` existe na base de um código. 

>[!NOTE]
>Se ativar o registo de nível de depuração para todos os espaços de nomes na aplicação, será cumprido pela todos os módulos em execução, incluindo `org.apache.http` mudar o nome como `com.microsoft.applicationinsights.core.dependencies.http`. Application Insights não conseguirá aplicar a filtragem para essas chamadas porque a chamada de registo está a ser efetuada através da biblioteca do Apache. Registo de nível de depuração produzir uma quantidade considerável de dados de registo e não é recomendado para instâncias de produção em direto.


## <a name="next-steps"></a>Passos seguintes
**Posso configurar Application Insights para a aplicação my server de Java. Pessoa que posso fazer?**

* [Monitorizar a disponibilidade das suas páginas web][availability]
* [Monitorizar a utilização da página web][usage]
* [Controlar a utilização e diagnosticar problemas nas suas aplicações de dispositivo][platforms]
* [Escrever código para controlar a utilização da sua aplicação][track]
* [Capturar os registos de diagnóstico][javalogs]

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md

