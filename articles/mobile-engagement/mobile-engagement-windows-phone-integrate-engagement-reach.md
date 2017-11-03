---
title: "Integração do SDK de alcance do Silverlight do Windows Phone"
description: "Como integrar o alcance do Azure Mobile Engagement para aplicações Windows Phone Silverlight"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0738f33df94d14fbb393bfaaf09e94c6560213cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Integração do SDK de alcance do Silverlight do Windows Phone
Tem de seguir o procedimento de integração descrito no [integração do Windows Phone Silverlight Engagement SDK](mobile-engagement-windows-phone-integrate-engagement.md) antes de seguir este guia.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Incorporar o SDK de alcançar o Engagement no seu projeto Windows Phone Silverlight
Não tem nada para adicionar. `EngagementReach`as referências e recursos já estão no seu projeto.

> [!TIP]
> Pode personalizar as imagens localizadas no `Resources` pasta do projeto, especialmente no ícone de marca (que predefinido para o ícone do Engagement).
> 
> 

## <a name="add-the-capabilities"></a>Adicionar as funcionalidades
SDK de alcançar o Engagement tem algumas capacidades adicionais.

Abra o `WMAppManifest.xml` de ficheiros e certifique-se de que são declaradas as seguintes capacidades:

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

A primeira é utilizada pelo serviço do MPNS para permitir a apresentação de notificação de alerta. O segundo é utilizada para incorporar uma tarefa do browser para o SDK.

Editar o `WMAppManifest.xml` de ficheiros e adicionar dentro de `<Capabilities />` etiqueta:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Ativar o serviço de notificação Push da Microsoft
Para poder utilizar o **serviço de notificação Push da Microsoft** (referido como MPNS) sua `WMAppManifest.xml` ficheiro tem de ter um `<App />` tag com um `Publisher` atributo definido como o nome do seu projeto.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializar o alcance do Engagement SDK
### <a name="engagement-configuration"></a>Configuração de envolvimento
A configuração de envolvimento é centralizada no `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar a configuração de alcance:

* *Opcional*, indicar se o push nativo (MPNS) está ativado ou não entre `<enableNativePush>` e `</enableNativePush>` etiquetas, (`true` por predefinição).
* *Opcional*, indicar o nome do canal push entre `<channelName>` e `</channelName>` etiquetas, forneça o mesmo que a aplicação pode atualmente utilizar ou deixe em branco.

Se pretender especificá-la no tempo de execução, pode chamar o método seguinte antes da inicialização de agente o Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> Pode especificar o nome do canal de push do MPNS da sua aplicação. Por predefinição, o Engagement cria um nome com base no appId. Não é necessário especificar o nome por si, exceto se planeia utilizar o canal de push fora o Engagement que tem.
> 
> 

### <a name="engagement-initialization"></a>Inicialização do engagement
Modificar o `App.xaml.cs`:

* Adicionar a sua `using` instruções:
  
      using Microsoft.Azure.Engagement;
* Inserir `EngagementReach.Instance.Init` apenas após `EngagementAgent.Instance.Init` no `Application_Launching` :
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Inserir `EngagementReach.Instance.OnActivated` no `Application_Activated` método:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> O `EngagementReach.Instance.Init` é executado num thread dedicado. Não é necessário fazê-lo por si.
> 
> 

## <a name="app-store-submission-considerations"></a>Considerações de submissão de arquivo de aplicação
Microsoft impõe algumas regras ao utilizar as notificações push:

A Microsoft [políticas de aplicações] documentação, secção 2.9:

1) Tem de pedir ao utilizador para aceitar para receber notificações push. Em seguida, as definições, adicione uma forma de desativar as notificações push.

O objeto de EngagementReach fornece dois métodos para gerir a recusa-na/de recusa, `EnableNativePush()` e `DisableNativePush()`. Pode, por exemplo, criar uma opção nas definições de com um botão de alternar para desactivar ou activar MPNS.

Também pode optar por desativar MPNS através da configuração o Engagement\<windows phone-sdk-alcance-configuração\>.

> 2.9.1) a aplicação tem primeiro de descrever as notificações devem ser fornecidas e **obter a permissão de utilizador rápida (optar ativamente por participar-in)**, e **tem de fornecer um mecanismo através do qual o utilizador pode ativamente por não receber as notificações push**. Todas as notificações fornecidas a utilizar o serviço de notificação Push da Microsoft tem de ser consistentes com a descrição fornecida para o utilizador e tem de cumprir todos os aplicável [políticas de aplicações] [ Content Policies] e [requisitos adicionais para tipos de aplicação específicos].
> 
> 

2) Não deve utilizar demasiados notificações push. Engagement processará as notificações por si.

> 2.9.2) a aplicação e a sua utilização do serviço de notificações de Push da Microsoft tem não excessivamente utilizar a capacidade de rede ou de largura de banda do serviço de notificações Push da Microsoft, ou caso contrário unduly burden Windows Phone ou outros dispositivos Microsoft ou serviço de notificações de push excessiva, conforme determinado pela Microsoft à sua discrição razoável e não pode prejudicar ou interferir com a quaisquer redes da Microsoft ou servidores ou quaisquer servidores de terceiros ou redes ligados ao serviço de notificação Push da Microsoft.
> 
> 

3) Não confie na MPNS para enviar informações de criticals. Engagement utiliza MPNS, pelo que também se aplica esta regra para as campanhas criadas dentro do Engagement front-end.

> 2.9.3) o serviço de notificações Push a Microsoft não pode ser utilizada para enviar notificações que são o missão crítico ou, caso contrário podem afetar é importante de vida ou death, incluindo sem notificações de limitação críticas relacionados com um dispositivo médicas ou condição. EXPRESSAMENTE A MICROSOFT EXCLUI QUAISQUER GARANTIAS QUE A UTILIZAÇÃO DO SERVIÇO DE NOTIFICAÇÃO PUSH DA MICROSOFT OU DE ENTREGA DE NOTIFICAÇÕES DE SERVIÇO DE NOTIFICAÇÃO PUSH DA MICROSOFT SERÁ INTERROMPIDA, ERRO LIVRE OU DE OUTRA FORMA GARANTIDA PARA OCORRER NUMA BASE EM TEMPO REAL.
> 
> 

**Não podemos garantir que a aplicação passará o processo de validação se não respeitem estas recomendações.**

## <a name="handle-data-push-optional"></a>Processar o push de dados (opcional)
Se pretender que a aplicação para poder receber alcance pushes de dados, terá de implementar dois eventos da classe EngagementReach:

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };

    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

Pode ver que a chamada de retorno de cada método devolve um valor booleano. Engagement envia um comentários para o back-end após a emissão de push de dados. Se a chamada de retorno devolve false, o `exit` comentários serão envio. Caso contrário, será `action`. Se nenhuma chamada de retorno está definida para os eventos, o `drop` comentários vai ser devolvido para o Engagement.

> [!WARNING]
> Engagement não é capaz de receber comentário múltiplos para um push de dados. Se pretender definir vários processadores por um evento, lembre-se de que os comentários serão a corresponder para a última enviada uma. Neste caso, é recomendável devolve sempre o mesmo valor para evitar que confuso comentários no front-end.
> 
> 

## <a name="customize-ui-optional"></a>Personalizar IU (opcional)
### <a name="first-step"></a>Primeiro passo
Iremos permitem-lhe personalizar o alcance da IU.

Para tal, tem de criar uma subclasse do `EngagementReachHandler` classe.

**Código de exemplo:**

    using Microsoft.Azure.Engagement;

    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

Em seguida, definir o conteúdo do `EngagementReach.Instance.Handler` campo com o seu objeto personalizado no sua `App.xaml.cs` classe dentro o `Application_Launching` método.

**Código de exemplo:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Por predefinição, o Engagement utiliza a suas próprias implementação `EngagementReachHandler`. Não tem de criar os seus próprios, e se o fizer, não tem de substituir cada método. É o comportamento predefinido para selecionar o objeto de base do Engagement.
> 
> 

### <a name="layouts"></a>Esquemas
Por predefinição, o alcance irá utilizar os recursos incorporados de DLL para apresentar as notificações e páginas.

No entanto, pode optar por utilizar os seus próprios recursos para refletir a sua marca destes componentes.

Pode substituir `EngagementReachHandler` métodos no seu subclasse para saber se o Engagement para utilizar as esquemas de:

**Código de exemplo:**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> O `CreateNotification` método pode devolver um valor nulo. A notificação não será apresentada e campanhas de alcance serão ignoradas.
> 
> 

Para simplificar a implementação de esquema, fornecemos também nosso própria xaml que possa servir como base para o seu código. Onde estão localizados num arquivo Engagement SDK (/ src/alcance /).

> [!WARNING]
> As origens que fornecemos são os exato mesmo que utilizamos. Por isso, se pretender modificá-los diretamente, não se esqueça de alterar o espaço de nomes e o nome.
> 
> 

### <a name="notification-position"></a>Posição de notificação
Por predefinição, uma notificação na aplicação é apresentada na parte inferior esquerda do lado da aplicação. Pode alterar este comportamento ao substituir o `GetNotificationPosition` método o `EngagementReachHandler` objeto.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Atualmente, pode escolher entre o `BOTTOM` (predefinição) e `TOP` posições.

### <a name="launch-message"></a>Inicie a mensagem
Quando um utilizador clica numa notificação do sistema (um alerta), o Engagement inicia a aplicação, carregar o conteúdo das mensagens push e apresentar a página da campanha correspondente.

Não há um atraso entre a iniciação da aplicação e a apresentação da página (consoante a velocidade da sua rede).

Para indicar ao utilizador que algo está a carregar, deve fornecer informações de um visual, como uma barra de progresso ou um indicador de progresso. Engagement não consegue processar que ela própria, mas fornece alguns processadores para si.

Para implementar a chamada de retorno, execute:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Pode definir a chamada de retorno na sua `Application_Launching` método de sua `App.xaml.cs` , preferencialmente, antes de ficheiros a `EngagementReach.Instance.Init()` chamada.

> [!TIP]
> Cada processador é chamado pelo Thread de IU. Não é necessário se preocupe quando utilizar uma MessageBox ou algo relacionadas com a IU.
> 
> 

[políticas de aplicações]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[requisitos adicionais para tipos de aplicação específicos]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

