---
title: "Separar a telemetria de desenvolvimento, teste e da versão no Azure Application Insights | Microsoft Docs"
description: "Obter telemetria direta aos recursos diferentes para os carimbos de desenvolvimento, teste e produção."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 8d95958bce0597bfb16ef1c6b99b72ce9134e66f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Separação de telemetria de desenvolvimento, teste e produção

Quando estiver a desenvolver a próxima versão de uma aplicação web, não pretender combinar cópias de segurança a [Application Insights](app-insights-overview.md) a telemetria da nova versão e a versão de lançamento já. Para evitar confusões, envie a telemetria de fases de desenvolvimento diferentes para separar recursos do Application Insights, com chaves de instrumentação separados (ikeys). Para tornar mais fácil alterar a chave de instrumentação como uma versão move de uma fase para outro, pode ser útil definir a ikey no código em vez de no ficheiro de configuração. 

(Se o sistema é um serviço de nuvem do Azure, há [outro método de configuração separadas ikeys](app-insights-cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sobre recursos e chaves de instrumentação

Quando configurar a monitorização do Application Insights para a sua aplicação web, cria um Application Insights *recursos* no Microsoft Azure. Abrir este recurso no portal do Azure para ver e analisar a telemetria recolhida da sua aplicação. O recurso é identificado por um *chave de instrumentação* (ikey). Quando instala o pacote de Application Insights para monitorizar a aplicação, pode configurá-lo com a chave de instrumentação, para que os que confie para onde enviar a telemetria.

Normalmente, optar por utilizar recursos separados ou um único recurso partilhado em cenários diferentes:

* Aplicações diferentes, independentes - utilize um recurso individual e ikey para cada aplicação.
* Utilizar vários componentes ou funções da aplicação de um negócio - um [único recurso partilhado](app-insights-monitor-multi-role-apps.md) para todas as aplicações de componente. Pode ser filtrada ou segmentada pela propriedade cloud_RoleName a telemetria.
* Desenvolvimento, teste e versão - utilizam um recurso individual e ikey para versões do sistema em 'Carimbo' ou a fase de produção.
* A | Testar B - utilizar um único recurso. Crie um TelemetryInitializer para adicionar uma propriedade para a telemetria que identifica os variantes.


## <a name="dynamic-ikey"></a>Chave de instrumentação dinâmica

Para tornar mais fácil alterar a ikey como o código move entre fases de produção, defini-lo no código em vez de no ficheiro de configuração.

Defina a chave de um método de inicialização, tais como global.aspx.cs num serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Neste exemplo, são colocadas ikeys para os recursos diferentes em diferentes versões do ficheiro de configuração web. Troca de ficheiro de configuração web - que pode fazê-lo como parte do script versão - será trocar o recurso de destino.

### <a name="web-pages"></a>Páginas Web
A iKey também é utilizada em páginas web da sua aplicação, no [script que recebeu do painel de início rápido](app-insights-javascript.md). Em vez de codificação-literalmente para o script, gerá-la a partir do Estado do servidor. Por exemplo, numa aplicação ASP.NET:

*JavaScript em Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Criar recursos adicionais do Application Insights
Para separar telemetria para os componentes da aplicação diferente ou para diferentes carimbos de data / (dev/teste/produção) do mesmo componente, em seguida, terá de criar um novo recurso do Application Insights.

No [portal.azure.com](https://portal.azure.com), adicione um recurso do Application Insights:

![Clicar em Novo, Application Insights](./media/app-insights-separate-resources/01-new.png)

* **Tipo de aplicação** afeta o que vê no painel de descrição geral e as propriedades disponíveis no [explorer métrica](app-insights-metrics-explorer.md). Se não vir o seu tipo de aplicação, escolha um dos tipos de web para páginas web.
* **Grupo de recursos** está para efeitos práticos para a gestão de propriedades, como [controlo de acesso](app-insights-resources-roles-access-control.md). Pode utilizar grupos de recursos separado para o desenvolvimento, teste e produção.
* **Subscrição** é a sua conta de pagamento no Azure.
* **Localização** é onde iremos manter os seus dados. Atualmente não pode ser alterada. 
* **Adicionar ao dashboard** coloca um mosaico de acesso de leitura rápida para o seu recurso na sua página de home page do Azure. 

Criar o recurso demora alguns segundos. Verá um alerta quando terminar.

(Pode escrever um [script do PowerShell](app-insights-powershell-script-create-resource.md) para criar um recurso automaticamente.)

### <a name="getting-the-instrumentation-key"></a>Obter a chave de instrumentação
A chave de instrumentação identifica o recurso que criou. 

![Clique em Essentials, clique na chave de instrumentação, CTRL + C](./media/app-insights-separate-resources/02-props.png)

Terá das chaves de instrumentação de todos os recursos aos quais a aplicação irá enviar dados.

## <a name="filter-on-build-number"></a>Filtrar por número de compilação
Quando publica uma nova versão da sua aplicação, deverá conseguir separar a telemetria a partir de diferentes compilações.

Pode definir a propriedade de versão da aplicação para que pode filtrar [pesquisa](app-insights-diagnostic-search.md) e [explorer métrica](app-insights-metrics-explorer.md) resultados.

![Uma propriedade de filtragem](./media/app-insights-separate-resources/050-filter.png)

Existem vários métodos diferentes de definir a propriedade de versão da aplicação.

* Defina diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Moldar essa linha num [inicializador de telemetria](app-insights-api-custom-events-metrics.md#defaults) para se certificar de que todas as instâncias de TelemetryClient estão definidas de forma consistente.
* [ASP.NET] Definir a versão `BuildInfo.config`. O módulo web selecionará a versão do nó BuildLabel. Incluir este ficheiro no seu projeto e não se esqueça de definir a propriedade cópia sempre no Explorador de soluções.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Gere automaticamente BuildInfo.config no MSBuild. Para tal, adicione alguns linhas ao seu `.csproj` ficheiro:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Isto gera um ficheiro chamado *Nomedoprojeto*. BuildInfo.config. O processo de publicação lhe mude o nome para BuildInfo.config.

    A etiqueta de compilação contém um marcador de posição (AutoGen _) quando criar com o Visual Studio. Mas, quando criadas com MSBuild, este é preenchido com o número de versão correta.

    Para permitir MSBuild gerar os números de versão, defina a versão como `1.0.*` no AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versão e controlo de versão
Para controlar a versão da aplicação, certifique-se de que `buildinfo.config` é gerado pelo processo do Microsoft Build Engine. No ficheiro .csproj, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Quando possui informações de compilação, o módulo Web do Application Insights adiciona automaticamente a **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Desta forma, poderá filtrar por versão quando executar [pesquisas de diagnóstico](app-insights-diagnostic-search.md) ou [explorar métricas](app-insights-metrics-explorer.md).

No entanto, tenha em atenção que o número de versão da compilação é gerado apenas pelo Microsoft Build Engine, não pela compilação do programador no Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se utilizar o Visual Studio Team Services, poderá [obter um marcador de anotação](app-insights-annotations.md) adicionado aos seus gráficos sempre que lançar uma nova versão. A imagem seguinte mostra como este marcador é apresentado.

![Captura de ecrã de um exemplo de anotação de versão num gráfico](./media/app-insights-asp-net/release-annotation.png)
## <a name="next-steps"></a>Passos seguintes

* [Recursos partilhados de várias funções](app-insights-monitor-multi-role-apps.md)
* [Criar um inicializador de telemetria para distinguir um | Variantes B](app-insights-api-filtering-sampling.md#add-properties)
