---
title: "Integração de SDK do Engagement de aplicações universais do Windows"
description: "Como integrar o Azure Mobile Engagement com aplicações universais do Windows"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 898160814304fa8ec65622056a77ca9d4caf2c99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Integração de SDK do Engagement de aplicações universais do Windows
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Este procedimento descreve a forma mais simples para ativar o Engagement análise e monitorização de funções na sua aplicação Universal do Windows.

Os seguintes passos são suficientes para ativar o relatório de registos necessários para todas as estatísticas relativas a utilizadores, sessões, atividades, as falhas e Technicals de computação. O relatório de registos necessários para outras estatísticas como eventos, erros e tarefas de computação tem de ser feito manualmente utilizando a API do Engagement (consulte [como utilizar o Mobile Engagement avançadas etiquetagem API na aplicação Windows Universal](mobile-engagement-windows-store-use-engagement-api.md) uma vez que estas estatísticas da aplicação dependente.

## <a name="supported-versions"></a>Versões suportadas
O Mobile Engagement SDK para aplicações universais do Windows apenas podem ser integradas no tempo de execução do Windows e aplicações da plataforma Universal do Windows filtragem:

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10 (famílias de ambiente de trabalho e dispositivos móveis)

> [!NOTE]
> Se tiver como objetivo o Windows Phone Silverlight, em seguida, consulte o [procedimento de integração do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Instalar o SDK de aplicações Universal do Mobile Engagement
### <a name="all-platforms"></a>Todas as plataformas
O Mobile Engagement SDK para a aplicação Universal do Windows está disponível como um pacote Nuget chamado *microsoftazure. Mobileengagement*. Pode instalá-lo do Visual Studio Gestor de pacotes Nuget.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8. x e Windows Phone 8.1
NuGet implementa automaticamente os recursos do SDK no `Resources` pasta na raiz do seu projeto de aplicação.

### <a name="windows-10-universal-windows-platform-applications"></a>Aplicações do Windows 10 plataforma Universal do Windows
NuGet não implementa automaticamente os recursos do SDK na aplicação UWP ainda. Terá de fazê-lo manualmente até que a implementação de recursos é reintroduced no NuGet:

1. Abra o Explorador de ficheiros.
2. Navegue para a seguinte localização (**x.x.x** é a versão do Engagement estiver a instalar): *% USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3. Arrastar e largar o **recursos** pasta a partir do Explorador de ficheiros para a raiz do projeto no Visual Studio.
4. No Visual Studio selecione o seu projeto e ativar o **Mostrar todos os ficheiros** ícone do **Explorador de soluções**.
5. Alguns ficheiros não estão incluídos no projeto. Para importá-los em simultâneo com o botão direito clique no **recursos** pasta, **excluir do projeto** e outra com o botão direito clique em de **recursos** pasta, **incluir no projeto** novamente incluir a pasta de toda. Todos os ficheiros do **recursos** pasta estão agora incluídas no seu projeto.

## <a name="add-the-capabilities"></a>Adicionar as funcionalidades
O Engagement SDK tem algumas funcionalidades do Windows SDK para que funcionem corretamente.

Abra o `Package.appxmanifest` de ficheiros e certifique-se de que são declaradas as seguintes capacidades:

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Inicializar o Engagement SDK
### <a name="engagement-configuration"></a>Configuração de envolvimento
A configuração de envolvimento é centralizada no `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar:

* A cadeia de ligação de aplicação entre as etiquetas `<connectionString>` e `<\connectionString>`.

Se pretender especificá-la no tempo de execução, pode chamar o método seguinte antes da inicialização de agente o Engagement:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

A cadeia de ligação para a sua aplicação é apresentada no Portal clássico do Azure.

### <a name="engagement-initialization"></a>Inicialização do engagement
Quando cria um novo projeto, um `App.xaml.cs` ficheiro é gerado. Esta classe herda `Application` e contém vários métodos importantes. Também irá ser utilizado para inicializar o SDK do Engagement.

Modificar o `App.xaml.cs`:

* Adicionar a sua `using` instruções:
  
      using Microsoft.Azure.Engagement;
* Defina um método para partilhar uma vez para todas as chamadas a inicialização do Engagement:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* Chamar `InitEngagement` no `OnLaunched` método:
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* Quando a aplicação é iniciada através de um esquema personalizado, outra aplicação ou da linha de comandos, em seguida, o `OnActivated` método é chamado. Também terá de iniciar o Engagement SDK quando a aplicação está ativada. Para tal, substituir `OnActivated` método:
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> É vivamente desencorajar-lhe adicionar a inicialização do Engagement no outro local da aplicação.
> 
> 

## <a name="basic-reporting"></a>Relatório básico
### <a name="recommended-method-overload-your-page-classes"></a>Recomendado método: Sobrecarga sua `Page` classes
Para ativar o relatório de todos os registos de que o Engagement para os utilizadores, sessões, atividades, as falhas e técnicas estatísticas de computação, basta pode efetuar todas as suas `Page` classes secundárias herdarem do `EngagementPage` classes.

Eis um exemplo de como fazê-lo para uma página da aplicação. Pode fazê-lo a mesma coisa para todas as páginas da sua aplicação.

#### <a name="c-source-file"></a>Ficheiro de origem do c#
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
> Se a sua página substitui o método `OnNavigatedTo`, certifique-se de que chama `base.OnNavigatedTo(e)`. Caso contrário, a atividade não será comunicada (o `EngagementPage` chama `StartActivity` dentro do respetivo método `OnNavigatedTo`).
> 
> 

#### <a name="xaml-file"></a>Ficheiro XAML
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

#### <a name="override-the-default-behaviour"></a>Substituir o comportamento predefinido
Por predefinição, o nome da classe da página é reportado como o nome de atividade, com nenhuma adicionais. Se a classe utiliza o sufixo "Página", o Engagement será também removê-lo.

Se pretende substituir o comportamento predefinido para o nome, basta adicione este para o seu código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Se pretender algumas informations adicionais com a atividade de relatório, pode adicionar este para o seu código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Estes métodos são chamados a partir do `OnNavigatedTo` método da sua página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chamar `StartActivity()` manualmente
Se não é possível ou não pretender sobrecarregar o `Page` classes, em vez disso, pode iniciar as atividades chamando `EngagementAgent` métodos diretamente.

Recomendamos chamar `StartActivity` dentro do `OnNavigatedTo` método da sua página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Certifique-se de que terminar a sessão corretamente.
> 
> O SDK Windows Universal automaticamente chama o `EndActivity` método quando a aplicação está fechada. Assim, é **altamente** recomendado para chamar o `StartActivity` método sempre que alterar a atividade do utilizador e a **nunca** chamar o `EndActivity` método, este método envia ao servidor de envolvimento que o utilizador atual tem de deixar a aplicação, esta será afeta todos os registos da aplicação.
> 
> 

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, pode querer eventos específicos de aplicação de relatório, erros e tarefas, para tal, utilize os outros métodos encontrados no `EngagementAgent` classe. O Engagement API permite utilizar todas as capacidades avançadas do Engagement.

Para obter mais informações, consulte [como utilizar o Mobile Engagement avançadas etiquetagem API na aplicação Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

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
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Impulsar modo
Por predefinição, os relatórios do serviço o Engagement os registos em tempo real. Se a aplicação comunica muito frequentemente os registos, é melhor para a memória intermédia os registos e para comunicá-los ao mesmo tempo em vez regular base (denominado "modo de rajada").

Para fazê-lo, chame o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor na **milissegundos**. Em qualquer altura, se pretender reativar o registo em tempo real, basta chame o método sem quaisquer parâmetros ou com o valor de 0.

O modo de rajada ligeiramente aumentar a vida bateria, mas tem um impacto no Monitor Engagement: duração de sessões e as tarefas de todos os será arredondado o limiar de rajada (assim, as sessões e as tarefas mais curtos do que o limiar de rajada poderá não estar visível). Recomenda-se para utilizar um limiar de rajada que 30000 (30s). Tem de ter em consideração que guardados registos estão limitados a 300 itens. Se a enviar é demasiado longa que pode perder algumas registos.

> [!WARNING]
> O limiar de rajada não pode ser configurado para um período inferiores a 1s. Se tentar fazê-lo, o SDK irá mostrar um rastreio com o erro e repõe automaticamente o valor predefinido, ou seja, 0s. Isto irá acionar o SDK para reportar os registos em tempo real.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

