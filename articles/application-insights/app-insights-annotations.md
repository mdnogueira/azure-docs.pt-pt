---
title: "Versão anotações para o Application Insights | Microsoft Docs"
description: "Adicionar implementação ou criar marcadores para os gráficos do Explorador de métricas no Application Insights."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: a479fa553d64f3820ae8513353484e72b57d30e4
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações em gráficos de métricas no Application Insights
Anotações no [Explorador de métricas](app-insights-metrics-explorer.md) gráficos mostram onde uma nova compilação ou outro evento significativo que implementou. Estes tornam mais fácil ver se as suas alterações tinham qualquer efeito no desempenho da aplicação. Podem ser criados automaticamente pelo [sistema de compilação do Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs). Também pode criar anotações para sinalizador qualquer evento como pelo [criá-los a partir do PowerShell](#create-annotations-from-powershell).

![Exemplo de anotações com visível correlação com tempo de resposta do servidor](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Anotações com VSTS compilação da versão

Versão anotações são uma funcionalidade de compilação baseado na nuvem e versão de serviço do Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Instale a extensão de anotações (uma vez)
Para conseguir criar anotações de versão, terá de instalar uma das extensões de serviço de equipa muitos disponíveis no mercado do Visual Studio.

1. Inicie sessão no seu [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) projeto.
2. No Visual Studio Marketplace, [a extensão de versão anotações](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)e adicione-o à sua conta de serviços da equipa.

![AT principais lado direito da página web de serviços da equipa, abra Marketplace. Selecione Visual Team Services e, em seguida, em e versão de compilação, escolha ver mais.](./media/app-insights-annotations/10.png)

Só precisa de fazer uma vez para a sua conta do Visual Studio Team Services. Versão anotações agora podem ser configuradas para qualquer projeto na sua conta. 

### <a name="configure-release-annotations"></a>Configurar anotações de versão

Tem de obter uma chave de API separada para cada modelo da versão VSTS.

1. Iniciar sessão para o [Portal do Microsoft Azure](https://portal.azure.com) e abra o recurso do Application Insights monitoriza a sua aplicação. (Ou [criar um agora](app-insights-overview.md), se ainda não o fez, ainda.)
2. Abra **acesso à API**, **Application Insights Id**.
   
    ![Em portal.azure.com, abra o recurso do Application Insights e escolha definições. Abra o acesso à API. Copie o ID da aplicação](./media/app-insights-annotations/20.png)

4. Numa janela separada do browser, abra (ou crie) o modelo de versão que gere as implementações de Visual Studio Team Services. 
   
    Adicionar uma tarefa e selecione a tarefa de anotação de versão do Application Insights a partir do menu.
   
    Colar o **Id da aplicação** que copiou do painel de acesso à API.
   
    ![No Visual Studio Team Services, abra a versão, selecione uma definição de versão e escolha Editar. Clique em Adicionar tarefa e selecione anotação de versão do Application Insights. Cole o ID do Application Insights.](./media/app-insights-annotations/30.png)
4. Definir o **APIKey** campo a uma variável `$(ApiKey)`.

5. Na janela do Azure, crie uma nova chave de API e efetuar uma cópia do mesmo.
   
    ![No painel de acesso à API na janela do Azure, clique em Criar chave de API. Fornecer um comentário, verifique anotações de escrita e clique em Gerar chave. Copie a chave de novo.](./media/app-insights-annotations/40.png)

6. Abra o separador de configuração do modelo de versão.
   
    Criar uma definição de variável para `ApiKey`.
   
    Cole a chave de API para a definição de variável ApiKey.
   
    ![Na janela Team Services, selecione o separador de configuração e clique em Adicionar variável. O nome do conjunto para ApiKey e no valor, cole a chave de que acabou de gerar e clique no ícone de bloqueio.](./media/app-insights-annotations/50.png)
7. Por fim, **guardar** a definição de versão.


## <a name="view-annotations"></a>Anotações de vista
Agora, sempre que utilizar o modelo de versão para implementar uma nova versão, será enviada uma anotação para o Application Insights. As anotações aparecerá nos gráficos no Explorador de métricas.

Clique em qualquer marcador de anotação para abrir os detalhes sobre o lançamento, incluindo o requerente, o ramo do controlo de origem, a versão de definição, o ambiente e muito mais.

![Clique em qualquer marcador de anotação de versão.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas do PowerShell
Também pode criar anotações a partir de qualquer processo como (sem utilizar VS Team System). 


1. Faça uma cópia local do [script do Powershell a partir do GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Obter o ID de aplicação e criar uma chave de API a partir do painel de acesso à API.

3. Chame o script seguinte:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

É fácil modificar o script, por exemplo, para criar anotações para passado.

## <a name="next-steps"></a>Passos seguintes

* [Criar itens de trabalho](app-insights-diagnostic-search.md#create-work-item)
* [Automatização com o PowerShell](app-insights-powershell.md)
