---
title: "Comparar o Azure Mobile Engagement com outros serviços do Azure semelhantes"
description: "Comparar o Azure Mobile Engagement com outros serviços do Azure semelhantes - HockeyApp, AppInsights, os Hubs de notificação"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7df2eb9ecebe3313dad9c15171552a084787f6b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Comparar o Azure Mobile Engagement com outros serviços do Azure semelhantes
A lista de serviços oferecidas pelo Microsoft Azure nunca é de expansão e por vezes poderá wonder como é diferente deste serviço que apenas de leitura ou ouvir sobre o Azure Mobile Engagement. Este artigo tenta limpar a confusão e direciona-lhe para escolher o Azure Mobile Engagement quando é mais adequado para a sua utilização. 

O Azure Mobile Engagement é um serviço direcionado especificamente **para os comerciantes/CMOs digitais** mas foi utilizada por qualquer **proprietário da aplicação móvel ou publicador** que pretende aumentar a utilização, retenção e monetization das respetivas aplicações móveis. 

*É uma plataforma de envolvimento do utilizador do software-como-um-serviço (SaaS) que fornece informações condicionada por dados acerca da utilização de aplicações, segmentação de utilizador em tempo real e permite que as notificações push com reconhecimento do contexto e mensagens na aplicação.* 

Eliminar baixo esta definição, temos as seguintes características de chaves que também realça a proposta de valor único:

1. **As notificações push com reconhecimento do contexto e mensagens na aplicação**
   
   Este é o foco de núcleo do produto - realizar notificações push segmentada e personalizados. E, para que isto acontecer, podemos recolher dados de análise comportamental avançado. 
2. **Condicionada por dados informações sobre a utilização da aplicação**
   
   Fornecemos plataforma SDKs para recolher a análise comportamental sobre os utilizadores da aplicação. Tenha em atenção a análise comportamental do prazo (as against análise de desempenho) porque iremos focar-se na forma como os utilizadores da aplicação estiver a utilizar a aplicação. Iremos recolher dados de análise de desempenho básico sobre erros, falhas, etc., mas que não o foco de núcleo do produto. 
3. **Segmentação de utilizador em tempo real**
   
   Depois de ter recolhidos dados de análise comportamental dos utilizadores da aplicação, iremos permitem-lhe segmento seu público-alvo com base em vários parâmetros e recolhidos dados para ativar a execução de campanhas push segmentada. 
4. **Software-como-um-serviço (SaaS):**
   
   Temos um portal separado do portal de gestão do Azure que está otimizado para interagir e ver a análise comportamental avançada sobre os utilizadores da aplicação e execute as campanhas push de marketing. O produto é adaptado para ajudá-lo a ficar sem tempo!   

Com este conjunto de diferenciação nos mão, eis como estamos a comparação com outras ofertas do Azure seemingly semelhantes - tenha em atenção que o artigo não uma comparação de nível de funcionalidade, mas demora um cenário mais baseada em abordagem para definir o produto funciona melhor:

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) é a solução de DevOps móvel da Microsoft. Com a mesma, pode distribuir compilações para beta testers, recolher dados de falhas e obter comentários dos utilizadores. Também é integrado com o Visual Studio Team Services, o que permite implementações de compilação mais fácil e integração do item de trabalho. 
   
   O foco aqui é DevOps e recolher dados de análise de desempenho sobre as aplicações móveis. Poderá acabar com a integração de ambos os HockeyApps e Mobile Engagement na sua aplicação e que não será possível invulgar porque, mesmo apesar de não existir algumas sobrepõem-se nos dados recolhidos, o foco principal dos produtos é diferente e que possam ajudam a alcançar os objetivos diferentes para a operação.  
2. [Application Insights](../application-insights/app-insights-overview.md) se a sua aplicação móvel tem um lado do servidor, em seguida, irá utilizar o Application Insights para monitorizar o lado do servidor web da sua aplicação, mas para aplicações móveis do lado do cliente, deve utilizar HockeyApp. 
3. [Os Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) fornece um serviço simples na medida em que não precisa de um SDK integrado na aplicação móvel e podem ter controlo total da sua aplicação móvel e permite o envio de notificações push com as capacidades de etiquetagem básicas. Esta é uma grande para qualquer proprietário de aplicação que cares menor sobre filtragem e mais sobre as informações de envio/comunicar de forma instantânea aos respetivos utilizadores de aplicação (difusão a uma população grande). 
   
   Tenha em atenção o foco durante o envio incrivelmente rápidas notificações com capacidade de segmentação básico. 

Vamos alguns cenários:

1. Tim faz parte da equipa de marketing digital no arquivo do Adventure Works que publica aplicações móveis para os utilizadores. Objetivo de TIM é certificar-se de que os utilizadores de transferirem as aplicações móveis continuam a utilizá-lo e com frequência. Aumenta não apenas uma marca anexar com os utilizadores de aplicações, mas também aumenta monetization quando os utilizadores da aplicação fazer compras utilizando a aplicação móvel. Para este Tim terá de enviar notificações de destino para os utilizadores das aplicações, que são úteis, para encorajá-los para abrir a aplicação e voltar a ele frequentemente. Este é onde Tim úteis Mobile Engagement. 
2. Joann faz parte da equipa de desenvolvimento de aplicações móveis em Adventure Works e está à procura de um produto obter detalhes sobre falhas, exceções, de registo e obter telemetria de desempenho a partir de uma aplicação. Este é onde Joann úteis HockeyApp. Joann foi integrar tanto HockeyApp para os cenários de programador concentra-se e o Azure Mobile Engagement para Tim na mesma aplicação para obter o máximo proveito de ambas. 
3. Round robin faz parte da equipa de desenvolvimento de aplicações móveis na rede Contoso notícias e todos os que pretende que está a enviar quebrar alertas de notícias de última hora para todos os utilizadores sem muito segmentação logo que é acionado o alerta de notícias de última hora. Este é onde Round Robin úteis Notification Hubs. 
   Neste cenário, é possível no entanto que porque a aplicação móvel evoluiu, há um requisito para fazer muito mais rica segmentação e obter informações sobre a aplicação do comportamento do utilizador. Neste momento, terá de avaliar o Azure Mobile Engagement Round Robin. 

Para recap, o objetivo do Mobile Engagement não se encontra apenas para recolher análise - não é "ainda outro produto de análise da Microsoft". É sobre o envio de notificações push segmentada e para esta filtragem, podemos recolher dados de análise comportamental mas o foco permanece no envio de notificações push que sentido a maioria para os utilizadores das aplicações para que não ser apresentados como spam. 

Para obter mais detalhes - observe isto [as vídeo rápida](mobile-engagement-overview.md) sobre Mobile Engagement num nutshell. 

