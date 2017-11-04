---
title: "Universal do Windows avançada de relatórios com o MobileApps Engagement"
description: "Como integrar o Azure Mobile Engagement com aplicações universais do Windows"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Avançadas de relatórios com o Engagement de aplicações universais do Windows SDK
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Este tópico descreve os cenários de relatórios adicionais na sua aplicação Universal do Windows. Estes cenários incluem opções que pode optar por aplicar a aplicação criada no [introdução](mobile-engagement-windows-store-dotnet-get-started.md) tutorial.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Antes de começar este tutorial, primeiro tem de concluir o [introdução](mobile-engagement-windows-store-dotnet-get-started.md) tutorial, o que é deliberadamente direta e simples. Este tutorial abrange opções adicionais, que pode escolher.

## <a name="specifying-engagement-configuration-at-runtime"></a>Especificar a configuração de envolvimento no tempo de execução
A configuração de envolvimento é centralizada no `Resources\EngagementConfiguration.xml` ficheiro do seu projeto, que é onde foi especificado no [introdução](mobile-engagement-windows-store-dotnet-get-started.md) tópico.

Mas também pode especificá-la no tempo de execução: pode chamar o método seguinte antes da inicialização de agente o Engagement:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Recomendado método: Sobrecarga sua `Page` classes
Para ativar o relatório de todos os registos de que o Engagement para os utilizadores, sessões, atividades, as falhas e técnicas estatísticas de computação, certifique-todos os seu `Page` classes secundárias herdam a `EngagementPage` classes.

Eis um exemplo de uma página da aplicação. Pode fazê-lo a mesma coisa para todas as páginas da sua aplicação.

### <a name="c-source-file"></a>Ficheiro de origem do c#
Modificar a sua página `.xaml.cs` ficheiro:

* Adicionar a sua `using` instruções:
  
      using Microsoft.Azure.Engagement;
* Substitua `Page` com `EngagementPage`:

**Sem o Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Com o Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> Se a sua página substitui o método `OnNavigatedTo`, certifique-se de que chama `base.OnNavigatedTo(e)`. Caso contrário, a atividade não é comunicada (o `EngagementPage` chamadas `StartActivity` dentro respetivo `OnNavigatedTo` método).
> 
> 

### <a name="xaml-file"></a>Ficheiro XAML
Modificar a sua página `.xaml` ficheiro:

* Adicione às suas instruções de espaços de nomes:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Substitua `Page` com `engagement:EngagementPage`:

**Sem o Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Com o Engagement:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Substituir o comportamento predefinido
Por predefinição, o nome da classe da página é reportado como o nome de atividade, com nenhuma adicionais. Se a classe utiliza o sufixo "Página", o Engagement remove-o.

Para substituir o comportamento predefinido para o nome, adicione este código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Para informações adicionais com a atividade de relatórios, adicione este código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Estes métodos são chamados a partir do `OnNavigatedTo` método da sua página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chamar `StartActivity()` manualmente
Se não é possível ou não pretender sobrecarregar o `Page` classes, em vez disso, pode iniciar as atividades chamando `EngagementAgent` métodos diretamente.

Recomendamos que chamar `StartActivity` dentro do `OnNavigatedTo` método da sua página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Certifique-se de que terminar a sessão corretamente.
> 
> O SDK Windows Universal automaticamente chama o `EndActivity` método quando a aplicação está fechada. Assim, é **altamente** recomendado para chamar o `StartActivity` método sempre que alterar a atividade do utilizador e a **nunca** chamar o `EndActivity` método. Este método notifica servidor o Engagement que o utilizador atual ter saído da aplicação, que irão afetar todos os registos da aplicação.
> 
> 

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, pode querer comunique eventos específicos de aplicação, erros e tarefas, para tal, utilizam os outros métodos encontrados no `EngagementAgent` classe. O Engagement API permite a utilização das capacidades avançadas de todos os Engagement.

Para obter mais informações, consulte [como utilizar o Mobile Engagement avançadas etiquetagem API na aplicação Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

