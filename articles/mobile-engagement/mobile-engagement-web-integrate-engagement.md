---
title: "Integração do SDK do Mobile Engagement Web do Azure | Microsoft Docs"
description: "As mais recentes atualizações e procedimentos para o SDK Web do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: 7d8eaa180e277741a583522ee62d68f5247b92bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrar o Azure Mobile Engagement numa aplicação web
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Os procedimentos neste artigo descrevem a forma mais simples para ativar a monitorização de funções no Azure Mobile Engagement na sua aplicação web e de análise.

Siga os passos para ativar os relatórios de registo que são necessárias para todas as estatísticas sobre utilizadores, sessões, atividades, as falhas e technicals de computação. Para estatísticas de aplicações dependentes, tais como eventos, erros e tarefas, tem de ativar os relatórios de registo manualmente utilizando a API do Azure Mobile Engagement. Para obter mais informações, saiba [como utilizar a marcação de API numa aplicação web de Mobile Engagement avançadas](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introdução
[Transferir o SDK da Web do Azure Mobile Engagement](http://aka.ms/P7b453).
O SDK do Mobile Engagement Web vem incluído como um único ficheiro JavaScript, Azure-engagement.js, que tem de incluir em cada página do seu site ou aplicação web.

> [!IMPORTANT]
> Antes de executar este script, tem de executar um fragmento de script ou um código de escrita para configurar o Mobile Engagement para a sua aplicação.
> 
> 

## <a name="browser-compatibility"></a>Compatibilidade com o browser
O SDK do Mobile Engagement Web utiliza JSON nativo e codificação e descodificação, além de pedidos de AJAX entre domínios (entidade confiadora na especificação W3C CORS). É compatível com os seguintes browsers:

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Configurar Mobile Engagement
Escrever um script que cria um global `azureEngagement` objeto JavaScript, como no exemplo seguinte. Porque o seu site poderá ter de páginas de múltiplos, este exemplo parte do princípio de que este script está incluído em todas as páginas. Neste exemplo, o objeto de JavaScript é denominado `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

O `connectionString` valor para a aplicação é apresentada no portal do Azure.

> [!NOTE]
> `appVersionName`e `appVersionCode` são opcionais. No entanto, recomendamos que configure-os para que a análise pode processar as informações de versão.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Incluir scripts de Mobile Engagement na sua páginas
Adicione scripts de Mobile Engagement às suas páginas de uma das seguintes formas:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Ou esta:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
Depois do script de SDK do Mobile Engagement Web estiver carregado, cria o **engagement** alias para aceder a APIs do SDK. Não é possível utilizar este alias para definir a configuração de SDK. Este alias é utilizado como uma referência nesta documentação.

Tenha em atenção que se o alias de predefinição está em conflito com outra variável global da sua página, pode redefini-lo na configuração da seguinte forma antes de carregar o SDK do Mobile Engagement Web:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Relatório básico
Relatórios básicos do Mobile Engagement abrange estatísticas de nível de sessão, por exemplo, estatísticas sobre utilizadores, sessões, atividades e falhas.

### <a name="session-tracking"></a>Controlo de sessão
Uma sessão de Mobile Engagement é dividida numa sequência de atividades, cada identificado por um nome.

Num Web site clássico, recomendamos que declare uma atividade diferente em cada página do seu site. Para uma Web site ou aplicação web no qual a página atual nunca é alterado, pode querer controlar as atividades numa escala mais pequena, tal como na página.

Qualquer forma, para iniciar ou alterar a atividade do utilizador atual, chame o `engagement.agent.startActivity` função. Por exemplo:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

O servidor de Mobile Engagement automaticamente termina uma sessão aberta em três minutos depois de fechar a página da aplicação.

Em alternativa, pode encerrar uma sessão manualmente ao chamar `engagement.agent.endActivity`. Isto define a atividade do utilizador atual para 'Inativo'.  A sessão vai terminar 10 segundos mais tarde, a menos que uma nova chamada para `engagement.agent.startActivity` retoma a sessão.

Pode configurar o atraso de 10 segundo o objeto de envolvimento global, da seguinte forma:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> Não é possível utilizar `engagement.agent.endActivity` no `onunload` chamada de retorno porque não é possível efetuar chamadas AJAX nesta fase.
> 
> 

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, se pretender comunique eventos específicos de aplicação, os erros e tarefas, terá de utilizar a API do Mobile Engagement. Aceder a API do Mobile Engagement através de `engagement.agent` objeto.

Pode aceder a todas as capacidades avançadas de Mobile Engagement na Mobile Engagement API. A API está detalhada no artigo [como utilizar a marcação de API numa aplicação web de Mobile Engagement avançadas](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personalizar os URLs utilizados para chamadas AJAX
Pode personalizar os URLs que utiliza o SDK do Mobile Engagement Web. Por exemplo, a redefinir o URL de registo (o SDK ponto final do registo), pode substituir a configuração da seguinte forma:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Se as suas funções de URL devolvem uma cadeia que comece com `/`, `//`, `http://`, ou `https://`, não é utilizado o esquema predefinido. Por predefinição, o `https://` esquema é utilizado para os URLs. Se pretender personalizar o esquema predefinido, substitua a configuração, como esta:

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
