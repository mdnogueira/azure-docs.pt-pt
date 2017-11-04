---
title: "Windows Phone Silverlight SDK procedimentos de atualização"
description: "Windows Phone Silverlight SDK procedimentos de atualização do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f87f65788075c7f4067e77946e1bcbc8f3709317
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK procedimentos de atualização
Se já tiver integrado uma versão mais antiga do nosso SDK na sua aplicação, terá de considerar os seguintes pontos ao atualizar o SDK.

Terá de seguir vários procedimentos, se omitido várias versões do SDK. Por exemplo, se a migração do 0.10.1 para 0.11.0 que tem de primeiro siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

## <a name="from-200-to-330"></a>De 2.0.0 para 3.3.0
### <a name="test-logs"></a>Registos do teste
Os registos de consola produzidos pelo SDK podem agora ser ativado/desativado/filtrados. Para personalizar esta, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um valor disponível no `EngagementTestLogLevel` enumeração, para a instância:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

## <a name="from-111-to-200"></a>De 1.1.1 para 2.0.0
O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS numa aplicação ligada ao Azure Mobile Engagement. 

> [!IMPORTANT]
> Capptain e Mobile Engagement não são os mesmos serviços e o procedimento indicado abaixo apenas realça como migrar a aplicação de cliente. Migrar o SDK na aplicação de mensagens em fila não de irá migrar os dados saídos de servidores Capptain para os servidores de Mobile Engagement
> 
> 

Se estiver a migrar de uma versão anterior, consulte o web site do Capptain migrar para 1.1.1 pela primeira vez, em seguida, aplique o procedimento seguinte

### <a name="nuget-package"></a>Pacote Nuget
Substitua **Capptain.WindowsPhone** por **microsoftazure. Mobileengagement** pacote Nuget.

### <a name="applying-mobile-engagement"></a>Aplicação de Mobile Engagement
O SDK utiliza o termo `Engagement`. Tem de atualizar o seu projeto para fazer corresponder esta alteração.

Tem de desinstalar o pacote atual do nuget Capptain. Considere-se de que todas as alterações na pasta Capptain recursos serão removidas. Se pretender manter esses ficheiros, em seguida, faça uma cópia dos mesmos.

Depois disso, instale o novo pacote nuget Microsoft Azure Engagement no seu projeto. Pode encontrá-lo diretamente no [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Esta ação substitui todos os ficheiros de recursos utilizados pelo Engagement e adiciona a DLL do Engagement novo para as referências do projeto.

Terá de limpar as referências do projeto, eliminando Capptain DLL referências. Se não fizer isto, a versão do Capptain entram em conflito e acontecerá de erros.

Se tiver personalizado Capptain recursos, copie o conteúdo de ficheiros antigo e cole-os no novos ficheiros do Engagement. Tenha em atenção que os ficheiros xaml e cs tem de ser atualizadas.

Quando esses passos são concluídos só tem de substituir as referências antigas do Capptain por novas referências de envolvimento.

1. Todos os espaços de nomes Capptain tem de ser atualizadas.
   
    Antes da migração:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Após a migração:
   
        using Microsoft.Azure.Engagement;
2. Todas as classes de Capptain que contêm "Capptain" devem conter ". o Engagement".
   
    Antes da migração:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Após a migração:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Para os ficheiros xaml Capptain espaço de nomes e atributos também alterar.
   
    Antes da migração:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Após a migração:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Para outros recursos, como imagens Capptain, tenha em atenção que estes também cujo nome foi mudados para utilizar "Envolvimento".

### <a name="application-id--sdk-key"></a>ID da aplicação / chave do SDK
Utiliza o engagement uma cadeia de ligação. Não tem de especificar um ID de aplicação e uma chave SDK Mobile engagement, só tem de especificar uma cadeia de ligação. Pode configurá-lo no seu ficheiro EngagementConfiguration.

A configuração de envolvimento pode ser definida em seu `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar:

* A cadeia de ligação de aplicação entre as etiquetas `<connectionString>` e `<\connectionString>`.

Se pretender especificá-la no tempo de execução, pode chamar o método seguinte antes da inicialização de agente o Engagement:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de ligação para a sua aplicação é apresentada no Portal clássico do Azure.

### <a name="items-name-change"></a>Alteração de nome de itens
Todos os itens com o nome *capptain* ter o nome *engagement*. Da mesma forma para *Capptain* para *Engagement*.

Exemplos de itens de Capptain frequentemente utilizadas:

* CapptainConfiguration agora o nome EngagementConfiguration
* CapptainAgent agora o nome EngagementAgent
* CapptainReach agora o nome EngagementReach
* CapptainHttpConfig agora o nome EngagementHttpConfig
* GetCapptainPageName agora o nome GetEngagementPageName

Tenha em atenção que mudar o nome também afeta substituído métodos.

