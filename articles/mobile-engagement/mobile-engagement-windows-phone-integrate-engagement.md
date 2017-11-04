---
title: "Integração de SDK do Engagement do Windows Phone Silverlight"
description: "Como integrar o Azure Mobile Engagement para aplicações Windows Phone Silverlight"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 447fea8d-f4e3-4ad4-8ec0-8e3cf1ad3ab0
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 29b18aecff783cebf617995e2a19f16f0b68b51b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-engagement-sdk-integration"></a>Integração de SDK do Engagement do Windows Phone Silverlight
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Este procedimento descreve a forma mais simples para ativar a análise do Azure Mobile Engagement e a monitorização de funções na sua aplicação do Windows Phone Silverlight.

Os seguintes passos são suficientes para ativar o relatório de registos necessários para todas as estatísticas relativas a utilizadores, sessões, atividades, as falhas e Technicals de computação. O relatório de registos necessários para outras estatísticas como eventos, erros e tarefas de computação tem de ser feito manualmente utilizando a API do Engagement (consulte [como utilizar o Mobile Engagement avançadas etiquetagem API na aplicação Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) abaixo), uma vez que estas estatísticas são dependentes da aplicação.

## <a name="supported-versions"></a>Versões suportadas
O Mobile Engagement SDK para Windows Silverlight só pode ser integrado em aplicações filtragem:

* Windows Phone 8.0
* Windows Phone 8.1 Silverlight

> [!NOTE]
> Se tiver como objetivo o Windows Phone 8.1 (não Silverlight), em seguida, consulte o [procedimento de integração Windows Universal](mobile-engagement-windows-store-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-silverlight-sdk"></a>Instalar o SDK do Mobile Engagement Silverlight
O Mobile Engagement SDK para Windows Silverlight está disponível como um pacote Nuget chamado *microsoftazure. Mobileengagement*. Pode instalá-lo do Visual Studio Gestor de pacotes Nuget. 

## <a name="add-the-capabilities"></a>Adicionar as funcionalidades
O Engagement SDK tem algumas funcionalidades do Windows Phone Silverlight SDK para que funcionem corretamente.

Abra o `WMAppManifest.xml` de ficheiros e certifique-se de que as seguintes capacidades declaradas no `Capabilities` painel:

* `ID_CAP_NETWORKING`
* `ID_CAP_IDENTITY_DEVICE`

## <a name="initialize-the-engagement-sdk"></a>Inicializar o Engagement SDK
### <a name="engagement-configuration"></a>Configuração de envolvimento
A configuração de envolvimento é centralizada no `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar:

* A cadeia de ligação de aplicação entre as etiquetas `<connectionString>` e `<\connectionString>`.

Se pretender especificá-la no tempo de execução, pode chamar o método seguinte antes da inicialização de agente o Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de ligação para a sua aplicação é apresentada no Portal clássico do Azure.

### <a name="engagement-initialization"></a>Inicialização do engagement
Quando cria um novo projeto, um `App.xaml.cs` ficheiro é gerado. Esta classe herda `Application` e contém vários métodos importantes. Também irá ser utilizado para inicializar o SDK do Engagement.

Modificar o `App.xaml.cs`:

* Adicionar a sua `using` instruções:
  
      using Microsoft.Azure.Engagement;
* Inserir `EngagementAgent.Instance.Init` no `Application_Launching` método:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
        EngagementAgent.Instance.Init();
      }
* Inserir `EngagementAgent.Instance.OnActivated` no `Application_Activated` método:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
      }

> [!WARNING]
> É vivamente desencorajar-lhe adicionar a inicialização do Engagement no outro local da aplicação. No entanto, tenha em atenção que o `EngagementAgent.Instance.Init` método executa um thread dedicado e não o thread de IU.
> 
> 

## <a name="basic-reporting"></a>Relatório básico
### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Recomendado método: Sobrecarga sua `PhoneApplicationPage` classes
Para ativar o relatório de todos os registos de que o Engagement para os utilizadores, sessões, atividades, as falhas e técnicas estatísticas de computação, basta pode efetuar todas as suas `PhoneApplicationPage` classes secundárias herdarem do `EngagementPage` classes.

Eis um exemplo de como fazê-lo para uma página da aplicação. Pode fazê-lo a mesma coisa para todas as páginas da sua aplicação.

#### <a name="c-source-file"></a>Ficheiro de origem do c#
Modificar a sua página `.xaml.cs` ficheiro:

* Adicionar a sua `using` instruções:
  
      using Microsoft.Azure.Engagement;
* Substitua `PhoneApplicationPage` com `EngagementPage` :

**Sem o Engagement:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Com o Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!WARNING]
> Se a sua página herda o `OnNavigatedTo` método, tenha cuidado permitir que o `base.OnNavigatedTo(e)` chamada. Caso contrário, a atividade não será reportada. Facto, o `EngagementPage` está a chamar `StartActivity` dentro de `OnNavigatedTo` método.
> 
> 

#### <a name="xaml-file"></a>Ficheiro XAML
Modificar a sua página `.xaml` ficheiro:

* Adicione à sua declarações de espaços de nomes:
  
      xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
* Substitua `phone:PhoneApplicationPage` com `engagement:EngagementPage` :

**Sem o Engagement:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Com o Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">

            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Substituir o comportamento predefinido
Por predefinição, o nome da classe da página é reportado como o nome de atividade, com nenhuma adicionais. Se a classe utiliza o sufixo "Página", o Engagement será também removê-lo.

Se pretende substituir o comportamento predefinido para o nome, basta adicione este para o seu código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Se pretender algumas informações adicionais com a atividade de relatório, pode adicionar este para o seu código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Estes métodos são chamados a partir do `OnNavigatedTo` método da sua página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chamar `StartActivity()` manualmente
Se não é possível ou não pretender sobrecarregar o `PhoneApplicationPage` classes, em vez disso, pode iniciar as atividades chamando `EngagementAgent` métodos diretamente.

Recomendamos que chamar `StartActivity` dentro do `OnNavigatedTo` método da sua PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [!IMPORTANT]
> Certifique-se de que terminar a sessão corretamente.
> 
> O SDK automaticamente chama o `EndActivity` método quando a aplicação está fechada. Assim, é **altamente** recomendado para chamar o `StartActivity` método sempre que alterar a atividade do utilizador e a **nunca** chamar o `EndActivity` método. Este método envia uma mensagem para o servidor de envolvimento que o utilizador atual deixou a aplicação e se este problema afeta todos os registos da aplicação.
> 
> 

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, pode querer eventos específicos de aplicação de relatório, erros e tarefas, para tal, utilize os outros métodos encontrados no `EngagementAgent` classe. O Engagement API permite utilizar todas as capacidades avançadas do Engagement.

Para obter mais informações, consulte [como utilizar o Mobile Engagement avançadas etiquetagem API na aplicação Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md).

## <a name="advanced-configuration"></a>Configuração avançada
### <a name="disable-automatic-crash-reporting"></a>Desativar o relatório de falhas automática
Pode desativar a funcionalidade do Engagement de relatórios de falhas automática. Em seguida, quando irá ocorrer uma exceção não processada, o Engagement não fazer nada.

> [!WARNING]
> Se planeia desativar esta funcionalidade, lembre-se de que quando irá ocorrer uma falha não processada na aplicação, Engagement não enviar as falhas **e** não irá fechar a sessão e tarefas.
> 
> 

Para desativar o relatório de falhas automática, basta Personalize a configuração, dependendo da forma que o declarado:

#### <a name="from-engagementconfigurationxml-file"></a>De `EngagementConfiguration.xml` ficheiro
Definir as falhas de relatório `false` entre `<reportCrash>` e `</reportCrash>` etiquetas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>De `EngagementConfiguration` objeto em tempo de execução
Definir falhas do relatório para false utilizando o seu objeto EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Impulsar modo
Por predefinição, os relatórios do serviço o Engagement os registos em tempo real. Se a aplicação comunica muito frequentemente os registos, é melhor para a memória intermédia os registos e para comunicá-los ao mesmo tempo em vez regular base (denominado "modo de rajada").

Para fazê-lo, chame o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor na **milissegundos**. Em qualquer altura, se pretender reativar o registo em tempo real, basta chame o método sem quaisquer parâmetros ou com o valor de 0.

O modo de rajada ligeiramente aumentar a vida bateria, mas tem um impacto no Monitor Engagement: duração de sessões e as tarefas de todos os será arredondado o limiar de rajada (assim, as sessões e as tarefas mais curtos do que o limiar de rajada poderá não estar visível). Recomenda-se para utilizar um limiar de rajada que 30000 (30s). Tem de ter em consideração que guardados registos estão limitados a 300 itens. Se a enviar é demasiado longa que pode perder algumas registos.

> [!WARNING]
> O limiar de rajada não pode ser configurado para um período menores que um segundo. Se tentar fazê, o SDK irá mostrar um rastreio com o erro e será reposto automaticamente para o valor predefinido, ou seja, zero segundos. Isto irá acionar o SDK para reportar os registos em tempo real.
> 
> 

