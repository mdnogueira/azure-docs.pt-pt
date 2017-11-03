---
title: 'Notification Hubs do Azure: Perguntas mais frequentes (FAQ) | Microsoft Docs'
description: "Perguntas mais frequentes na conceção/implementação de soluções de Hubs de notificação"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "notificação push, notificações push, notificações push de iOS, notificações android push, ios push, android push"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
ms.openlocfilehash: f3bfda250b7f8ed679d05057682b95683d402535
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Push notificações com Notification Hubs do Azure: Perguntas mais frequentes
## <a name="general"></a>Geral
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>O que é a estrutura de recurso dos Hubs de notificação?

Os Hubs de notificação do Azure tem dois níveis de recurso: hubs e espaços de nomes. Um concentrador é um recurso de push único que pode conter as informações de push da plataforma de uma aplicação. Um espaço de nomes é uma coleção de hubs de uma região.

Mapeamento recomendado corresponde a um espaço de nomes com uma aplicação. Num espaço de nomes, pode ter um concentrador de produção que funciona com a sua aplicação de produção, um concentrador de teste que funciona com a aplicação de teste e assim sucessivamente.

### <a name="what-is-the-price-model-for-notification-hubs"></a>O que é o modelo de preços de Hubs de notificação?
Os detalhes de preços mais recente podem ser encontrados no [preços de Hubs de notificação] página. Os Notification Hubs é faturada ao nível do espaço de nomes. (Para a definição de um espaço de nomes, consulte o artigo "Qual é a estrutura de recurso dos Notification Hubs?") Os Notification Hubs oferece três camadas:

* **Livre**: esta camada é um ponto de partida para explorar as capacidades de push. Não é recomendado para aplicações de produção. Obter 500 dispositivos e milhões de 1 pushes incluído por espaço de nomes por mês, com a nenhuma garantia (SLA) de contrato de nível de serviço.
* **Básico**: esta camada (ou o escalão Standard) é recomendado para aplicações de produção mais pequenas. Obter 200 000 dispositivos e 10 milhões pushes incluído por espaço de nomes por mês como uma linha de base. Opções de crescimento de quota são incluídas.
* **Standard**: esta camada é recomendada para médio para aplicações de produção de grandes dimensões. Obter 10 milhões de dispositivos e 10 milhões pushes incluído por espaço de nomes por mês como uma linha de base. Quota aumento opções e avançada telemetria capacidades são incluídas.

Funcionalidades do escalão Standard:
* **Telemetria completa**: pode utilizar os Notification Hubs por mensagem de telemetria para controlar quaisquer pedidos de push e comentários de sistema de notificação de plataforma para a depuração.
* **Arquitetura "multitenancy"**: pode trabalhar com as credenciais do sistema de notificação de plataforma num nível espaço de nomes. Esta opção permite-lhe facilmente divididas inquilinos em hubs no mesmo espaço de nomes.
* **Agendadas push**: pode agendar a enviar notificações para ser enviado em qualquer altura.

### <a name="what-is-the-notification-hubs-sla"></a>O que é o SLA de Hubs de notificação?
Escalões básico e padrão dos Notification Hubs, de aplicações está corretamente configuradas podem enviar notificações push ou efetuar operações de gestão do registo a, pelo menos, 99,9% do tempo. Para obter mais informações sobre o SLA, vá para o [SLA de Hubs de notificação](https://azure.microsoft.com/support/legal/sla/notification-hubs/) página.

> [!NOTE]
> Porque as notificações push dependem de sistemas de notificação de plataforma de terceiros (como Apple APNS e Google FCM), não há nenhuma garantia SLA para a entrega destas mensagens. Depois dos Notification Hubs envia os lotes para sistemas de notificação de plataforma (SLA garantida), é da responsabilidade dos sistemas de notificação de plataforma para fornecer os pushes (nenhum SLA garantida).

### <a name="which-customers-are-using-notification-hubs"></a>Os clientes estão a utilizar os Notification Hubs?
Muitos clientes utilizam os Notification Hubs. Alguns dos relevantes estão listados aqui:

* Sochi 2014: Centenas de grupos de interesses, 3 + milhões de dispositivos e notificações de 150 + milhões distribuídas nas duas semanas. [Caso prático: Sochi]
* Skanska: [caso prático: Skanska]
* Tempos de Seattle: [caso prático: tempos de Seattle]
* Mural.LY: [caso prático: Mural.ly]
* 7Digital: [caso prático: 7Digital]
* Aplicações Bing: Dezenas de milhões de dispositivos enviam 3 milhões de notificações por dia.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Como atualizar ou mudar o meu hub ou espaço de nomes para uma camada diferente?
Vá para o  **[portal do Azure]** > **espaços de nomes de Hubs de notificação** ou **Notification Hubs**. Selecione o recurso que pretende atualizar e aceda a **escalão de preço**. Tenha em atenção os seguintes requisitos:

* O escalão de preço atualizado aplica-se a *todos os* hubs no espaço de nomes que está a trabalhar.
* Se a contagem de dispositivo excede o limite da camada que está a desatualização para, terá de eliminar os dispositivos antes de a mudar.


## <a name="design-and-development"></a>Conceção e desenvolvimento
### <a name="which-server-side-platforms-do-you-support"></a>Que plataformas do lado do servidor de suportar?
SDKs de servidor estão disponíveis para .NET, Java, Node.js, PHP e Python. APIs de Hubs de notificação baseiam-se em interfaces REST, para que possa trabalhar diretamente com as APIs REST, se estiver a utilizar plataformas diferentes ou não quiser dependência adicional. Para obter mais informações, visite o [APIs REST do Notification Hubs] página.

### <a name="which-client-platforms-do-you-support"></a>Que plataformas de cliente suportar?
Notificações push são suportadas para [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [China android (através do Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) e [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [aplicações do Chrome](notification-hubs-chrome-push-notifications-get-started.md)e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Para obter mais informações, visite o [tutoriais de Notification Hubs introdução] página.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Suportam mensagem de texto, correio eletrónico ou notificações de web?
Hubs de notificação foi concebido principalmente para enviar notificações para aplicações móveis. Não fornece texto ou e-mail capacidades de mensagens. No entanto, as plataformas de terceiros que fornecem estas capacidades podem ser integradas com Notification Hubs para enviar notificações push nativo com [Mobile Apps].

Os Notification Hubs não fornecem também um serviço de entrega de notificações push baseadas no browser a box. Os clientes podem implementar esta funcionalidade utilizando SignalR sobre as plataformas suportadas do lado do servidor. Se pretender enviar notificações para aplicações de browser na Chrome sandbox, consulte o [tutorial de aplicações do Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Como são Mobile Apps e relacionadas com Notification Hubs do Azure e quando utilizá-los?
Se tiver uma existente aplicação móvel de volta fim e pretender adicionar apenas a capacidade para enviar notificações push, pode utilizar os Notification Hubs do Azure. Se pretender configurar a sua aplicação móvel novamente fim a partir do zero, considere utilizar a funcionalidade de Mobile Apps do App Service do Azure. Uma aplicação móvel Aprovisiona automaticamente um notification hub para que pode enviar facilmente notificações push de back-end da aplicação móvel. Preços para Mobile Apps incluem os custos de base de um hub de notificação. Paga apenas quando exceder os pushes incluídos. Para obter mais detalhes sobre os custos, vá para o [preços do App Service] página.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Quantos dispositivos posso suportar se enviar notificações push através dos Notification Hubs?
Consulte o [preços de Hubs de notificação] página para obter detalhes sobre o número de dispositivos suportados.

Se necessitar de suporte para mais de 10 milhões de dispositivos registados, [contacte-nos](https://azure.microsoft.com/overview/contact-us/) diretamente e iremos ajudar a dimensionar a sua solução.

### <a name="how-many-push-notifications-can-i-send-out"></a>O número de notificações push pode enviar?
Consoante a camada selecionada, Notification Hubs do Azure automaticamente ajusta-se com base no número de notificações que fluem através do sistema.

> [!NOTE]
> O custo de utilização geral pode aumentar com base no número de notificações push que está a ser servido. Certifique-se de que está em consideração os limites de camada descritos no [preços de Hubs de notificação] página.
> 
> 

Os nossos clientes utilizam os Notification Hubs para enviar milhões de notificações push diariamente. Não é necessário fazer nada especiais para dimensionar o alcance das notificações push, desde que estiver a utilizar Notification Hubs do Azure.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Quanto tempo demora para enviar notificações para alcançar o meu dispositivo push?
Num cenário de utilização normal, em que a carga a receber é consistente e até mesmo, os Notification Hubs do Azure pode processar pelo menos *notificação push de milhões de 1 envia um minuto*. Esta taxa pode variar consoante o número de etiquetas, a natureza envia a entrada e de outros fatores externos.

Durante a hora de entrega estimado, o serviço calcula os destinos por plataforma e mensagens de rotas para o serviço de notificação Push (PNS) com base no etiquetas registadas ou expressões de etiqueta. É da responsabilidade do PNS para enviar notificações para o dispositivo.

O PNS garante qualquer SLA para entrega de notificações. No entanto, a maioria das notificações push são entregues nos dispositivos de destino dentro de alguns minutos (normalmente, dentro de 10 minutos) desde o momento em que são enviadas para os Notification Hubs. Algumas notificações poderão demorar mais tempo.

> [!NOTE]
> Os Hubs de notificação do Azure tem uma política implementada para remover quaisquer notificações push que não são fornecidas para o PNS dentro de 30 minutos. Este atraso pode acontecer por diversas razões, mas a maioria das normalmente porque o PNS é a limitação da aplicação.
> 
> 

### <a name="is-there-any-latency-guarantee"></a>Existe qualquer garantia de latência?
Devido à natureza de notificações push (que são fornecidas por um PNS externo, específicas da plataforma), não há nenhuma garantia de latência. Normalmente, a maioria das notificações push são entregues dentro de alguns minutos.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>O que é necessário a ter em consideração quando conceber uma solução com os hubs de notificação e espaços de nomes?
#### <a name="mobile-appenvironment"></a>Ambiente/aplicações móvel
* Utilize um hub de notificação por aplicação móvel, por ambiente.
* Num cenário de multi-inquilino, cada inquilino deve ter um concentrador separado.
* Nunca partilhar o mesmo hub de notificação para produção e ambientes de teste. Esta prática poderá causar problemas quando enviar notificações. (O Apple oferece Sandbox e produção Push pontos finais, cada um com credenciais em separado).
* Por predefinição, pode enviar notificações de teste para os seus dispositivos registados através do portal do Azure ou o componente integrado do Azure no Visual Studio. O limiar é definido para 10 dispositivos que são selecionados aleatoriamente entre o conjunto de registo.

> [!NOTE]
> Se o seu hub foi originalmente configurada com um certificado de sandbox Apple e, em seguida, foi reconfigurado para utilizar um certificado de produção da Apple, os tokens de dispositivo original são inválidos. Tokens inválidos causam pushes falhar. Separar os seus ambientes de produção e teste e utilizar os hubs de diferentes para os diferentes ambientes.
> 
> 

#### <a name="pns-credentials"></a>Credenciais PNS
Quando uma aplicação móvel está registada no portal do Programador de uma plataforma (por exemplo, da Apple ou da Google), um tokens de segurança e identificador de aplicação são enviados. O aplicação de back-end fornece estes tokens para PNS a plataforma para que podem ser enviadas notificações push para dispositivos. Tokens de segurança podem ter o formato de certificados (por exemplo, Apple iOS ou Windows Phone) ou chaves de segurança (por exemplo, Google Android ou Windows). Tem de ser configurados no hubs de notificação. Configuração é geralmente feita ao nível do hub de notificação, mas também pode ser feito ao nível do espaço de nomes num cenário de multi-inquilino.

#### <a name="namespaces"></a>Espaços de nomes
Espaços de nomes podem ser utilizados para o agrupamento de implementação. Também pode ser utilizados para representar todos os hubs de notificação para todos os inquilinos da mesma aplicação num cenário de multi-inquilino.

#### <a name="geo-distribution"></a>Distribuição de Georreplicação
Distribuição Georreplicação não é sempre crítica em cenários de notificação push. Vários PNSes (por exemplo, o APNS ou o GCM) que entregar notificações push para dispositivos não são distribuídos uniformemente.

Se tiver uma aplicação que é utilizada global, pode criar hubs em diferentes espaços de nomes ao utilizar o serviço de Notification Hubs em diferentes regiões do Azure em todo o mundo.

> [!NOTE]
> Não recomendamos esta disposição porque aumenta os custos de gestão, especialmente para registos. Deve ser feita apenas se existir uma necessidade explícita.
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>Devo fazer registos de back-end da aplicação ou diretamente através do cliente de dispositivos?
Registos de back-end da aplicação são úteis quando tiver de autenticar clientes antes de criar o registo. Se estiver a também útil quando tiver tags que tem de ser criados ou modificados por aplicação back-end com base na lógica da aplicação. Para obter mais informações, visite o [orientações de registo de back-end] e [orientações de registo de back-end 2] páginas.

### <a name="what-is-the-push-notification-delivery-security-model"></a>O que é o modelo de segurança de entrega de notificações push?
Os Hubs de notificação do Azure utiliza um [assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md)-baseada em modelo de segurança. Pode utilizar os tokens de assinatura de acesso partilhado ao nível do espaço de nomes de raiz ou ao nível do hub de notificação granular. Assinatura de acesso tokens podem ser definidos a seguir regras de autorização diferentes, por exemplo, para enviar as permissões de mensagem ou para escutar de permissões de notificação de partilhado. Para obter mais informações, consulte o [modelo de segurança de Notification Hubs] documento.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Forma como deve processar os payload confidencial em notificações push?
Todas as notificações são fornecidas aos dispositivos de destino por PNS da plataforma. Quando é enviada uma notificação para Notification Hubs do Azure, é processado e transmitido para o respetivo PNS.

Todas as ligações do remetente para os Notification Hubs do Azure para o PNS, utilizam HTTPS.

> [!NOTE]
> Os Hubs de notificação do Azure não regista o payload de mensagens de qualquer forma.
> 
> 

Para enviar payloads confidenciais, recomendamos que utilize um padrão de proteger Push. O remetente oferece uma notificação de ping com um identificador de mensagem para o dispositivo sem o payload confidencial. Quando a aplicação no dispositivo recebe o payload, a aplicação chama uma API segura diretamente ao obter os detalhes da mensagem. Para obter um guia sobre como implementar este padrão, vá para o [tutorial Notification Hubs Secure Push] página.

## <a name="operations"></a>Operações
### <a name="what-support-is-provided-for-disaster-recovery"></a>O suporte é fornecido para a recuperação de desastre?
Fornecemos cobertura de recuperação após desastre de metadados do nosso lado (o nome de Hubs de notificação, a cadeia de ligação e outras informações críticas). Quando é acionado um cenário de recuperação após desastre, dados de registo são o *apenas segmentar* da infraestrutura de Hubs de notificação que é perdida. Terá de implementar uma solução para repovoar estes dados para a novo pós-recuperação de hub:

1. Crie um hub de notificações secundário num centro de dados diferentes. Recomendamos que crie um desde o início para proteger a partir de um evento de recuperação de desastres que pode afetar as capacidades de gestão. Também pode criar um momento do evento de recuperação após desastre.

2. Preencha o hub de notificação secundário com os registos do seu hub de notificação primário. Não recomendamos a tentar manter registos em ambos os hubs e mantê-las em sincronização conforme registos são fornecidos em. Esta prática não funciona corretamente devido a tendência inerente de registos para expirar no lado PNS. Os Notification Hubs limpa-los como receber comentários PNS sobre registos expirou ou é inválidos.  

Temos dois recomendações para o back-ends de aplicação:

* Utilize uma aplicação de back-end que mantém um determinado conjunto de registos no respetivo fim. Em seguida, pode efetuar uma inserção em massa para o hub de notificação secundário.

* Utilize uma aplicação de back-end que obtém as informações de regular de registos do hub de notificação primário como uma cópia de segurança. Em seguida, pode efetuar uma inserção em massa para o hub de notificação secundário.

> [!NOTE]
> Funcionalidade de exportação/importação de registos disponível no escalão Standard está descrita com os [exportação/importação de registos] documento.
> 
> 

Se não tiver um back-end, quando a aplicação for iniciada em dispositivos de destino, execute um novo registo no hub de notificação secundário. Eventualmente o hub de notificação secundária tem todos os dispositivos de Active Directory registados.

Haverá um período de tempo quando os dispositivos com aplicações não abertas não irão receber notificações.

### <a name="is-there-audit-log-capability"></a>Existe capacidade do registo de auditoria?
Todas as operações de gestão de Notification Hubs Ir para os registos de operações, que estão expostos no [portal clássico do Azure].

## <a name="monitoring-and-troubleshooting"></a>Monitorização e resolução de problemas
### <a name="what-troubleshooting-capabilities-are-available"></a>Estão disponíveis que capacidades de resolução de problemas?
Os Hubs de notificação do Azure fornece várias funcionalidades para resolução de problemas, especialmente para o cenário mais comuns de notificações ignorados. Para obter mais informações, consulte o [resolução de problemas de Notification Hubs] documento técnico.

### <a name="what-telemetry-features-are-available"></a>Quais as funcionalidades de telemetria estão disponíveis?
Azure Notification Hubs ativa a ver dados de telemetria no [portal clássico do Azure]. Estão disponíveis detalhes das métricas no [métricas de Hubs de notificação] página.

> [!NOTE]
> Notificações de êxito significam simplesmente que foi entregar notificações push o PNS externo (por exemplo, APNS para Apple) ou o GCM para o Google. É da responsabilidade do PNS para fornecer as notificações para os dispositivos de destino. Normalmente, o PNS não expõe as métricas de entrega para terceiros.  
> 
> 

Fornecemos também a capacidade para exportar os dados de telemetria programaticamente (no escalão Standard). Para obter mais informações, consulte o [exemplo de Notification Hubs métricas].

[portal clássico do Azure]: https://manage.windowsazure.com
[preços de Hubs de notificação]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Caso prático: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Caso prático: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Estudo de caso: Horas de Seattle]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Caso prático: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Caso prático: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[APIs REST do Notification Hubs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[tutoriais de Notification Hubs introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[tutorial de aplicações do Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[orientações de registo de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[orientações de registo de back-end 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[modelo de segurança de Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[tutorial Notification Hubs Secure Push]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[resolução de problemas de Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[métricas de Hubs de notificação]: https://msdn.microsoft.com/library/dn458822.aspx
[exemplo de Notification Hubs métricas]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[exportação/importação de registos]: https://msdn.microsoft.com/library/dn790624.aspx
[portal do Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[preços do App Service]: https://azure.microsoft.com/pricing/details/app-service/
