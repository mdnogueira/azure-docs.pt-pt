---
title: "Guias de resolução de problemas do Azure Mobile Engagement"
description: "Guia de resolução de problemas do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 93b5e3f4892f974bf9df28955956136528470e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement - Guia de Resolução de Problemas
## <a name="introduction"></a>Introdução
O guia de resolução de problemas seguinte irão ajudá-lo a compreender as causas raiz dos alguns problemas normalmente visualizados e irá ativar a resolver no seu próprio. 

## <a name="general"></a>Geral
Em geral, deve sempre garantir o seguinte:

1. Certifique-se de que já leu todos os passos necessários para integração conforme descrito no nosso [tutoriais de introdução](mobile-engagement-windows-store-dotnet-get-started.md)
2. Estão a utilizar a versão mais recente da plataforma SDKs. 
3. Testar o um dispositivo real e um emulador porque alguns problemas são específicos para apenas o emulador. 
4. Não são atingir os limites/limitações do Mobile Engagement que estão documentadas [aqui](../azure-subscription-service-limits.md)
5. Se não for possível estabelecer ligação com o back-end do Mobile Engagement serviço ou ver dados não carregados continuamente, em seguida, certifique-se de que existem incidentes sem interrupção contínua do serviço verificando [aqui](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problemas de 'Monitor'
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Não estou a ver o meu dispositivo ser apresentado no separador Monitor
Separador Monitor mostra os dispositivos ligados à sua plataforma de Mobile Engagement em tempo real. Se está a depurar um emulador e o dispositivo, em seguida, deverá ver, pelo menos, uma sessão aqui. Se a aplicação tiver sido distribuída, em seguida, verá o medidor de sessões ativas refletir os dispositivos que estão ligados à plataforma em tempo real. 

Se não vir o dispositivo no separador monitorização, em seguida, é provável que um problema de integração do SDK. Alguns passos comuns para tomar para resolver-se da seguinte forma:

1. Certifique-se de que está a utilizar a cadeia de ligação correta na aplicação móvel e é a secção de chaves do SDK e não a secção chaves de API. A cadeia de ligação liga-se a sua aplicação móvel para a instância da aplicação de Mobile Engagement na qual irá ver o seu dispositivo no separador Monitor. 
2. Para a plataforma do Windows - se a sua página substitui o `OnNavigatedTo` método, certifique-se de que chama `base.OnNavigatedTo(e)`.
3. Se integrar o Mobile Engagement para uma aplicação móvel existente, em seguida, pode também garantir que não são falta quaisquer passos observando os passos de integração avançadas [aqui](mobile-engagement-windows-store-integrate-engagement.md)
4. Certifique-se de que está a enviar, pelo menos, um ecrã/atividade através da substituição a página com EngagementActivity consoante a plataforma que estão a funcionar conforme descrito no [tutoriais de introdução](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Estou a ver o separador Monitor que mostra uma sessão, mesmo quando posso ter desligado ou fechado a minha aplicação / emulador.
Se for a única ligada à plataforma neste momento e estiver a utilizar um emulador para abrir a aplicação, em seguida, isto é provavelmente devido a caraterísticas emulador. Em geral, tem de garantir que, volte ao ecrã principal no emulador para a sessão de aplicação desligar com êxito. Além disso, na plataforma do Windows, durante a depuração com o Visual Studio, poderá ter de garantir que no Visual Studio, pode aceder a **eventos de ciclo de vida** barra de menus e clique em **suspender** realmente fechar o sessão. Consulte [Windows tutorial](mobile-engagement-windows-store-dotnet-get-started.md) para obter mais detalhes. 

## <a name="analytics-issues"></a>Problemas de 'Analytics'
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Posso não estou a ver todos os dados / atualizar dados no separador de análise
Dados de análise são recalculados regulares e pode demorar até 24 horas para esta atualização. Estes dados não estão em tempo real e irão ver a atualizar a dentro deste período de 24 horas.
Certifique-se no entanto que estiver a enviar, pelo menos, um ecrã ou atividade para o back-end de plataforma por uma substituição, pelo menos, uma página com `EngagementActivity` ou chamar `SendActivity` explcitly. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Estou a ver incorretamente capturada data/hora para um dispositivo no separador de análise
O período de tempo para a análise baseia-se a data a partir das definições de dispositivo dos utilizadores. Por isso, certifique-se de que o dispositivo tem a data definida corretamente. 

## <a name="segment-issues"></a>Problemas de 'Segmento'
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Criar um segmento e é ser apresentado como a cinzento ou não é possível mostrar todos os dados
Criação de segmento não se encontra em tempo real de momento. Esta é calculada ao mesmo tempo que os dados de análise são agregados e, de modo pode demorar até 24 horas. Deve verificar novamente mais tarde mas entretanto deve também garantir que as suas aplicações móveis, de facto, estão a enviar os dados on the basis of que são que formam os segmentos. Por exemplo, Se um evento indicar 'foo' não está a ser enviado por todos os dispositivos móveis, em seguida, conseguiriam quaisquer dados do segmento para um segmento criado com o EventName = foo como critério. Também deve verificar a integração do SDK para garantir que a sua aplicação móvel está a enviar os dados corretamente. 

## <a name="reach-or-push-notifications-issues"></a>Problemas de 'Alcançar' ou notificações Push
### <a name="my-push-messages-are-not-being-delivered"></a>Não estão a ser entregue meu mensagens push
1. Tente enviar notificações para um dispositivo de teste pela primeira vez para se certificar de que todos os componentes - aplicação móvel, o SDK e o serviço consegue entregar notificações push e ligada corretamente. 
2. Enviar sempre a 'fora da aplicação notificação' mais simples primeiro através de uma campanha que não está agendada e nem tem quaisquer critério de audiência especificado. Isto é novamente provar a conectividade de notificação está a funcionar corretamente. 
3. Se estiver a ter problemas na entrega de notificações na aplicação, em seguida, também é um boa primeiro passo para tentar enviar uma notificação fora da aplicação pela primeira vez. 
4. Certifique-se de que o 'Push nativo' está configurado corretamente para a sua aplicação móvel. Consoante a plataforma envolvem-será ou chaves (Android, Windows) ou certificados (iOS). Consulte [Interface de utilizador - definições](mobile-engagement-user-interface-settings.md)
5. Fora da aplicação, as notificações também foi bloqueadas pelo utilizador através do sistema operativo móvel, por isso, certifique-se que não for este o caso. 
6. Certifique-se de que não está a definir o *ignorar audiência; push será enviado aos utilizadores através da API* opção o **campanha** campanha de secção um alcance porque tal irá garantir que as notificações push só pode ser enviadas através de APIs. 
7. Certifique-se de que está a testar a sua campanha push com ambos os um dispositivo ligado através de Wi-Fi e phone operador de rede para eliminar a ligação de rede como uma origem dos problemas possíveis.
8. Certifique-se de que a data/hora do sistema no seu dispositivo/emulador está correta porque qualquer dispositivo sincronizado também irá interferir com a capacidade do serviço de notificações Push entrega de notificações. 

Plataforma mais específica de resolução de problemas de instruções abaixo:

1. **iOS** 
   
   * Certifique-se de que os certificados são válidas e não expiradas para notificações Push do iOS. 
   * Certifique-se de que o utilizador é corretamente configurar um *produção* certificado na sua aplicação de Mobile Engagement. 
   * Certifique-se de que está a testar num *dispositivos físicos reais.* O simulador iOS não consegue processar mensagens push.
   * Certifique-se de que o identificador do pacote está configurado corretamente na aplicação móvel. Consulte as instruções [aqui](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * Quando o testar, utilize a distribuição de "Ad Hoc" no seu perfil de aprovisionamento móvel. Não poderá receber notificação se a aplicação é compilada através de uma "Depuração"
2. **Android**
   
   * Certifique-se de que especificou o número de projeto correto no ficheiro de AndroidManifest.xml da sua aplicação móvel que é seguido \n caráter. 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Certifique-se de que não estão em falta ou incorretamente configuradas as permissões no ficheiro de manifesto do Android 
   * Certifique-se de que o número de projeto que está a adicionar à sua aplicação de cliente é da mesma conta em que recebeu a chave do servidor GCM. Qualquer erro de correspondência entre os dois irá impedir que os pushes vai. 
   * Se o sistema está a receber notificações, mas não na aplicação, em seguida, reveja o [especificar um ícone para a secção de notificações](mobile-engagement-android-get-started.md) como provavelmente não especificar o ícone correto no ficheiro de manifesto do Android. 
   * Se está a enviar uma notificação de BigPicture, em seguida, certifique-se de que, se tiver servidores de imagem externo, em seguida, têm de ser capaz de suporte HTTP "GET" e "HEAD".
3. **Windows**
   
   * Certifique-se de que associou a aplicação com uma aplicação da loja Windows válida. No Visual Studio – terá de clique com o botão direito do rato em projeto e selecione a opção "Associar com loja de aplicações" e selecione a aplicação que criou na loja Windows. Esta aplicação da loja Windows deve ser o mesmo a partir de onde obteve as credenciais de push nativo para configurar no portal do Mobile Engagement.
   * Se está a receber notificações push fora da aplicação, mas não nas notificações na aplicação com `EngagementOverlay` integração, em seguida, certifique-se existe um elemento de grelha de raiz na sua página. EngagementOverlay utiliza o primeiro elemento de "Grelha" que se encontra no ficheiro xaml para adicionar o web duas vistas da página. Se pretender localizar onde serão definidas vistas web, pode definir uma grelha com o nome "EngagementGrid", no entanto, terá de Certifique-se existe suficiente altura e largura para a web subsequentes duas vistas que mostrará a notificação e o anúncio seguinte como notificação da aplicação:
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Criar um notificação de push/anúncio/campanha e, mesmo depois-enviar-me a notificação, é apresentada como 'Active'. O que significa?
O **campanha** que criou no Mobile Engagement é chamado pelo porque é um significado de notificação push longa como ligam de novos dispositivos à sua plataforma de envolvimento móvel, estes serão automaticamente enviados a notificação Aqui, configure, desde satisfazem os critérios definidos na campanha. Este é não é uma configuração de notificação único captura uma. Terá de manualmente, clique em de **concluir** botão para terminar a campanha para que não enviar mais notificações. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Criar uma campanha de emissão e posso estou receção das notificações com êxito no entanto sempre que abrir a cópia de segurança da aplicação, receber a notificação do mesma mesmo posso tinha alvo de ação-lo antes de?
Isto é provavelmente acontecer durante os testes e se estiver a utilizar emuladores ou alguns framework como TestFlight de teste. O que acontece, é aqui que em todas as aplicações de executar a instância, é adquirir um novo DeviceID e enviar para o nosso back-end que está a causar a plataforma de Mobile Engagement tratá-los como um novo dispositivo e enviar a notificação. 

## <a name="getting-support"></a>Obter suporte
Se não for possível resolver o problema por si, em seguida, que pode:

1. Procure o seu problema em threads existentes no fórum de StackOverflow e [fórum MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) e se não, em seguida, colocar uma pergunta não existe. 
2. Se encontrar uma funcionalidade em falta, em seguida, adicionar/votos para o pedido no nosso [fórum do UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Se tiver Microsoft suporta aberto um incidente de suporte, fornecendo os seguintes detalhes: 
   * ID de subscrição do Azure
   * Plataforma (por exemplo, iOS, Android etc)
   * ID da aplicação
   * ID de campanha (para problemas de notificação push)
   * ID do dispositivo
   * Versão do SDK do Mobile Engagement (por exemplo, o Android SDK v2.1.0)
   * Detalhes do erro com a mensagem de erro exata e o cenário

