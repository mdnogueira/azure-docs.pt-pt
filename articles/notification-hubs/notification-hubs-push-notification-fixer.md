---
title: "Notification Hubs do Azure - diretrizes de diagnóstico"
description: "Orientações sobre como diagnosticar problemas comuns com Notification Hubs do Azure."
services: notification-hubs
documentationcenter: Mobile
author: ysxu
manager: erikre
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 32e3a2e6f840afd865375a622cfae0d33ba65090
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs---diagnosis-guidelines"></a>Hubs de notificação do Azure – diretrizes de diagnóstico
## <a name="overview"></a>Descrição geral
Uma das perguntas mais comuns que recebemos dos clientes de Notification Hubs do Azure é a forma como para saber por que motivo não veem uma notificação enviada a partir do seu back-end da aplicação aparecer no dispositivo cliente - onde e por que razão as notificações foram removidas e como corrigir este problema. Neste artigo, irá passar por várias razões, razão pela qual as notificações podem obter removidas ou não ficar nos dispositivos. Podemos também irá procurar através de formas em que pode analisar e descobrir a causa raiz. 

Primeiro de tudo, é fundamental compreender a forma como Notification Hubs do Azure envia notificações para os dispositivos.
![][0]

Um fluxo de notificação envio típica, a mensagem é enviada do **back-end da aplicação** para **Azure Notification Hub (dos NH)** que por sua vez tem algumas processamento em todos os registos tendo em conta as etiquetas configuradas & expressões de etiqueta para determinar o "destinos" ou seja, todos os os registos que necessitam para receber a notificação push. Estes registos podem abranger em qualquer um ou todos os nossas as plataformas suportadas - iOS, Google, Windows, Windows Phone, Kindle e Baidu para Android na China. Depois dos destinos são estabelecidos, dos NH, em seguida, pushes saída notificações, dividir por vários lote de registos, para a plataforma de dispositivo específico **serviço de notificação Push (PNS)** -por exemplo, o APNS para Apple, o GCM para Google etc. Dos NH autentica com o respetivo PNS com base nas credenciais definido no Portal clássico do Azure, na página configurar o Notification Hub. O PNS reencaminha, então, as notificações para o respetivo **dispositivos cliente**. Esta é a plataforma recomendada para entregar notificações push e tenha em atenção que a fase final da entrega de notificações ocorre entre a plataforma PNS e o dispositivo. Assim que poderemos ter quatro componentes principais - *cliente*, *back-end da aplicação*, *Azure Notification Hubs (dos NH)* e *serviços de notificação Push (PNS)* e qualquer uma destas pode provocar notificações obter removidas. Obter mais detalhes sobre esta arquitetura não está disponível na [descrição geral dos Notification Hubs].

Falha ao entregar notificações pode ocorrer durante o teste/teste inicial fase que pode indicar um problema de configuração ou este poderá acontecer na produção onde todos ou alguns das notificações podem ser obter ignorados com a indicação de algumas aplicações mais aprofundada ou problema de padrão de mensagens. A secção abaixo veremos entre comuns do tipo rarer, que algumas das quais pode encontrar óbvios e outros não, tanto que vários cenários de notificações ignorados. 

## <a name="azure-notifications-hub-mis-configuration"></a>Configuração incorrecta do Hub de notificações do Azure
Os Hubs de notificação do Azure tem de se autenticar perante no contexto da aplicação do programador para conseguir com êxito enviar notificações para o respetivo PNS. Isto é possibilitado pela programadores criar uma conta de programador com a respetiva plataforma (Google, Apple, Windows etc) e, em seguida, registar a sua aplicação onde obter credenciais que têm de ser configurados no portal na secção de configuração de Notification Hubs. Se estiver a efetuar não existem notificações através de, primeiro passo deve ser para se certificar de que as credenciais corretas são configuradas no Notification Hub correspondência destes com a aplicação criada na respetiva conta de programador específicas da plataforma. Encontrará nosso [obter tutoriais iniciado] útil para abordar este processo de uma forma de passo a passo. Seguem-se algumas configurações incorrecta comuns:

1. **Geral**
   
    um) certifique-se de que o nome de hub de notificação (sem erros de digitação) é o mesmo:
   
   * Onde está a registar do cliente, 
   * Onde está a enviar notificações de back-end,  
   * Onde configurou as credenciais PNS e 
   * Cujas credenciais SAS que configurou no cliente e o back-end. 
     
     b) certifique-se de que está a utilizar as cadeias de configuração de SAS corretas no cliente e o back-end da aplicação. Como uma regra geral, tem de utilizar o **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** no back-end de aplicação (o que lhe dá a permissão para enviar a notificação para dos NH)
2. **Configuração de Push serviço Apple Notification (APNS)**
   
    Tem de manter dois hubs – um para produção e outra para testes objetivo. Isto significa que carregar o certificado que vai utilizar no ambiente de sandbox para um hub separado e o certificado que vai utilizar na produção a um concentrador separado. Não tente carregar diferentes tipos de certificados no mesmo hub como pode causar falhas de notificação para baixo a linha. Se der por si numa posição onde inadvertidamente carregou diferentes tipos de certificado para o mesmo hub, recomenda-se para eliminar o hub e começar do início. Se por alguma razão, não conseguiu eliminar o hub, em seguida, no muito menor, tem de eliminar todos os registos existentes do hub. 
3. **Configuração do Google Cloud Messaging (GCM)** 
   
    um) certifique-se de que pretende ativar "Google Cloud Messaging para Android" sob o projeto de nuvem. 
   
    ![][2]
   
    b) certifique-se de que cria um "chave de servidor" ao obter as credenciais que dos NH irá utilizar para autenticar com o GCM. 
   
    ![][3]
   
    c) certifique-se de que configurou "ID do projeto" no cliente, que é uma entidade inteiramente numérica que pode obter a partir do dashboard:
   
    ![][1]

## <a name="application-issues"></a>Problemas de aplicações
1) **Etiquetas / expressões de etiqueta**

Se estiver a utilizar etiquetas ou expressões de etiqueta para segmentar o público-alvo, é sempre possível que quando está a enviar a notificação, não há nenhum destino que está a ser encontrado com base em expressões de etiquetas/tag que estiver a especificar a chamada de envio. É melhor rever os registos para se certificar de que existem etiquetas que correspondem ao enviar a notificação e, em seguida, certifique-se a receção de notificações apenas a partir de clientes com esses registos. Por exemplo, Se todos os seus registos dos NH foram efetuados com diga a etiqueta "Política" e está a enviar uma notificação com etiqueta de "Desporto", não será enviada a todos os dispositivos. Caso complexo pode incluir expressões de etiqueta onde apenas registou com "A etiqueta" ou "Tag B", mas durante o envio de notificações, está a filtrar "Tag A e e etiqueta B". Na secção diagnosticar automática sugestões abaixo, existem formas em que pode rever os registos, juntamente com as etiquetas têm. 

2) **Problemas de modelo**

Se estiver a utilizar modelos e certifique-se de que está a seguir as diretrizes descritas no [orientações de modelo]. 

3) **Registos inválidos**

Pressupondo que o Hub de notificação foi configurado corretamente e quaisquer expressões de etiquetas/tag foram utilizadas resultavam corretamente a procura de destinos válidos para o qual as notificações devem receber, dos NH desencadeado desativar várias de processamento de lotes em paralelo - cada lote de envio de mensagens para um conjunto de registos. 

> [!NOTE]
> Uma vez que tal é o processamento em paralelo, iremos não garante a ordem pela qual as notificações serão entregues. 
> 
> 

Agora o Hub de notificações do Azure está otimizado para um modelo de entrega de mensagem de "no máximo uma vez". Isto significa que tentarmos uma duplicação de Desativação, para que não existem notificações são fornecidas mais do que uma vez num dispositivo. Para garantir que este examine os registos e certifique-se de que apenas um é enviado pelo identificador do dispositivo antes de enviar a mensagem para o PNS. Como cada lote é enviado para o PNS, que por sua vez está a aceitar e validar os registos, é possível que o PNS Deteta um erro com um ou mais registos num batch, devolve um erro dos NH de Azure e interrompe o processamento deste modo, remover esse batch completamente. Isto é especialmente verdade com APNS que utiliza um protocolo de fluxo TCP. Embora que estão otimizados para a maioria depois de entrega, neste caso, remova o registo faulting da nossa base de dados e, em seguida, repita a entrega da notificação para o resto dos dispositivos que batch.

Pode obter informações de erro para a tentativa falhada de entrega em relação a um registo com as APIs REST do Azure Notification Hubs: [por mensagem de telemetria: obter telemetria de mensagem de notificação](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [PNS comentários](https://msdn.microsoft.com/library/azure/mt705560.aspx). Consulte o [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) por exemplo de código.

## <a name="pns-issues"></a>Problemas PNS
Assim que a mensagem de notificação foi recebida pelo respetivo PNS, em seguida, a responsabilidade de entrega da notificação para o dispositivo. Os Hubs de notificação do Azure está fora da imagem aqui e não tem nenhum controlo no quando ou se a notificação vai ser entregue no dispositivo. Uma vez que os serviços de notificação de plataforma pretty robustos, notificações tendem a aceder os dispositivos o PNS a partir de dentro de alguns segundos. Se o PNS contudo é a limitação, em seguida, Notification Hubs do Azure aplicam-se um back exponencial desativar estratégia e se o PNS permanece inacessível para 30 minutos, em seguida, temos uma política no local para expirar e remover permanentemente as mensagens. 

Se um PNS tentativas enviar uma notificação, mas o dispositivo estiver offline, a notificação é armazenada pelo PNS para um período de tempo limitado e fornecida ao dispositivo quando ficar disponível. Apenas uma notificação recente para uma determinada aplicação está armazenada. Se várias notificações são enviadas enquanto o dispositivo estiver offline, cada nova notificação faz com que a notificação anterior sejam eliminadas. Este comportamento manter apenas a notificação mais recente é referido como União de notificações no APNS e collapsing no GCM (que utiliza uma chave collapsing). Se o dispositivo irá permanecer offline durante muito tempo, são eliminadas quaisquer notificações que foram que está a ser armazenadas para o mesmo. Origem - [orientações APNS] & [orientações do GCM]

Com os Notification Hubs do Azure - pode transmitir uma chave de agregação através de um cabeçalho HTTP utilizando o genérico `SendNotification` API (por exemplo, para o .NET SDK – `SendNotificationAsync`) que assume também os cabeçalhos de HTTP que são transmitidos como é o respetivo PNS. 

## <a name="self-diagnose-tips"></a>Sugestões de diagnosticar automática
Aqui iremos irá examinar a vários meios para diagnosticar e raiz causam problemas de Hub de notificação:

### <a name="verify-credentials"></a>Verificar credenciais
1. **Portal do programador PNS**
   
    Certifique-se do respetivo PNS programador portal (WNS, APNS, GCM, etc.) utilizando a nossa [obter tutoriais iniciado].
2. **Portal clássico do Azure**
   
    Aceda ao separador Configurar para rever e corresponder às credenciais com os obtidos a partir do portal de programador PNS. 
   
    ![][4]

### <a name="verify-registrations"></a>Verifique os registos
1. **Visual Studio**
   
    Se utilizar o Visual Studio para o desenvolvimento pode ligar ao Microsoft Azure e ver e gerir um bunch dos serviços do Azure, incluindo o Hub de notificações "No Explorador de servidores". Isto é principalmente útil para o seu ambiente de desenvolvimento/teste. 
   
    ![][9]
   
    Pode ver e gerir todos os registos do seu hub será são categorizados para plataforma, de modo nativa ou registo de modelo, qualquer etiquetas, PNS identificador, id de registo e a data de expiração. Também pode editar um registo no momento - que é útil diga se pretender editar qualquer etiquetas. 
   
    ![][8]
   
   > [!NOTE]
   > Visual Studio funcionalidades para editar registos só devem ser utilizada durante dev/teste com um número limitado de registos. Caso seja não existe uma necessidade para corrigir os registos em volume, considere a utilização de exportação/importação funcionalidade de registo descrita aqui - [exportação/importação de registos](https://msdn.microsoft.com/library/dn790624.aspx)
   > 
   > 
2. **Explorador do Service Bus**
   
    Muitos clientes utilizam ServiceBus explorer descrito aqui - [Explorador de ServiceBus] para ver e gerir o seu hub de notificação. É um projeto de código aberto disponível a partir do code.microsoft.com - [código do Explorador de barramento de serviço]

### <a name="verify-message-notifications"></a>Certifique-se de notificações de mensagens
1. **Portal Clássico do Azure**
   
    Pode aceder ao separador "Depuração" para enviar notificações de teste para os seus clientes sem necessitar de qualquer back-end do serviço cópia de segurança e em execução. 
   
    ![][7]
2. **Visual Studio**
   
    Também pode enviar notificações de teste de comforts do Visual Studio:
   
    ![][10]
   
    Pode ler mais sobre aqui - a funcionalidade de explorer do Visual Studio Notification Hub do Azure 
   
   * [Descrição geral do Explorador de servidor VS]
   * [Mensagem de blogue de Explorador de servidor VS - 1]
   * [Mensagem de blogue de Explorador de servidor VS - 2]

### <a name="debug-failed-notifications-review-notification-outcome"></a>Depurar falhas notificações / reveja o resultado da notificação
**Propriedade de EnableTestSend**

Enviar uma notificação através de Notification Hubs, inicialmente apenas obtém colocado em fila cópias de segurança dos NH fazer processamento para descobrir a todos os respetivos destinos e, em seguida, eventualmente dos NH envia-a para o PNS. Isto significa que quando estiver a utilizar a REST API ou de qualquer cliente SDK, o retorno com êxito a chamada de envio apenas significa que a mensagem tem foi com êxito em fila cópias de segurança com o Notification Hub. -Não atribua uma aprofundadas sobre o que aconteceu quando dos NH eventualmente obteve enviar a mensagem para PNS. Se a sua notificação não está a chegar ao dispositivo cliente, é possível que, quando dos NH tentou entregar a mensagem à PNS, Ocorreu um erro, por exemplo, o tamanho do payload excedeu o máximo permitido pelo PNS ou as credenciais configuradas dos NH são etc inválido. Para obter informações sobre os erros de PNS, ter introduzimos uma propriedade denominada [EnableTestSend funcionalidade]. Esta propriedade é ativada automaticamente quando enviar mensagens de teste do portal ou de cliente do Visual Studio e, por conseguinte, permite-lhe ver informações detalhadas de depuração. Pode utilizá-lo através de APIs de colocar o exemplo do SDK .NET onde se encontram disponíveis agora e será adicionado a todos os SDKs do cliente eventualmente. Para utilizar esta opção com a chamada REST, acrescente simplesmente um parâmetro de cadeia de consulta chamado "teste" no final do seu envio chamada por exemplo 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Exemplo (.NET SDK)*

Suponha que está a utilizar o .NET SDK para enviar uma notificação de alerta nativo:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);

`result.State`irá simplesmente estado `Enqueued` no final da execução sem qualquer aprofundadas sobre o que aconteceu ao seu push. Agora, pode utilizar o `EnableTestSend` booleana propriedade ao inicializar o `NotificationHubClient` e pode obter o estado detalhado sobre os erros PNS encontrado ao enviar a notificação. A chamada de envio aqui demorará algum tempo para devolver porque está a devolver apenas depois dos NH tem entregue a notificação PNS para determinar o resultado. 

    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Saída de exemplo*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Esta mensagem indica a credenciais inválidas estão configuradas no hub de notificação ou um problema com os registos no hub e o decorrer recomendado seria eliminar este registo e permitir que o cliente recriá-lo antes de enviar a mensagem. 

> [!NOTE]
> Tenha em atenção que a utilização desta propriedade é muito limitada e, por isso, tem de utilizar isto apenas ambiente de desenvolvimento/teste com um conjunto limitado de registos. Iremos enviar apenas notificações de depuração para 10 dispositivos. Além disso, temos um limite de processamento de depuração envia ser 10 por minuto. 
> 
> 

### <a name="review-telemetry"></a>Reveja a telemetria
1. **Utilizar o Portal clássico do Azure**
   
    O portal permite-lhe obter uma descrição geral de todas as atividades em seu Hub de notificação. 
   
    a) no separador "dashboard", pode ver uma vista de agregados dos registos, notificações, bem como de erros por plataforma. 
   
    ![][5]
   
    b) também pode adicionar vários outras plataforma métricas específicas no separador "Monitor de" para observe mais profundo particularmente quaisquer erros específicos de PNS devolvido quando dos NH tentar enviar a notificação para o PNS. 
   
    ![][6]
   
    c) deve começar por ler o **mensagens a receber**, **operações de registo**, **notificações com êxito** e, em seguida, aceda a por separador de plataforma para rever os erros específicos de PNS. 
   
    d) se tiver o hub de notificação configurado incorretamente com as definições de autenticação, em seguida, verá o erro de autenticação PNS. Este é um bom indicador para verificar as credenciais PNS. 

2) **Acesso programático**

Obter mais detalhes aqui- 

* [Acesso programático telemetria]
* [Acesso de telemetria através de exemplo APIs] 

> [!NOTE]
> Vários telemetria relacionada com funcionalidades como **registos de exportação/importação**, **telemetria acesso através de APIs** etc apenas estão disponíveis no escalão Standard. Se tentar utilizar estas funcionalidades, se estiver na gratuito ou o escalão básico, em seguida, irá receber a mensagem de exceção para este efeito ao utilizar o SDK e um HTTP 403 (proibido) quando utilizá-los diretamente a partir de REST APIs. Certifique-se de que tem de mover até padrão camada através do Portal clássico do Azure.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[descrição geral dos Notification Hubs]: notification-hubs-push-notification-overview.md
[obter tutoriais iniciado]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[orientações de modelo]: https://msdn.microsoft.com/library/dn530748.aspx 
[orientações APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[orientações do GCM]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[Explorador de ServiceBus]: http://msdn.microsoft.com/library/dn530751.aspx
[código do Explorador de barramento de serviço]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Descrição geral do Explorador de servidor VS]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Mensagem de blogue de Explorador de servidor VS - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Mensagem de blogue de Explorador de servidor VS - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend funcionalidade]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Acesso programático telemetria]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Acesso de telemetria através de exemplo APIs]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

