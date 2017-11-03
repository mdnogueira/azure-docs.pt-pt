---
title: "Do Azure Mobile Engagement guia - APIs de resolução de problemas"
description: "Guias do Azure Mobile Engagement - APIs de resolução de problemas"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
ms.openlocfilehash: a7ae0a83046f2d67b790f672dcd3ae261987357a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-api-issues"></a>Guia de resolução de problemas para problemas de API
Seguem-se possíveis problemas que pode surgir com como administradores interagem com o Azure Mobile Engagement através de APIs.

## <a name="syntax-issues"></a>Problemas de sintaxe
### <a name="issue"></a>Problema
* Erros de sintaxe com a API (ou um comportamento inesperado).

### <a name="causes"></a>Causas
* Problemas de sintaxe:
  * Certifique-se de que verifique a sintaxe de API específica que está a utilizar para confirmar que a opção está disponível.
  * Um problema comum verificado com a utilização da API é confunda a API de alcance e a API de Push de mensagens em fila (a maioria das tarefas que devem ser efetuadas com a API de alcance em vez da API de Push). 
  * Outro problema comuns com a integração do SDK e a utilização da API é confunda a chave do SDK e a chave de API.
  * Scripts que se ligam às APIs a precisam de enviar dados, pelo menos, a cada 10 minutos ou a ligação será o tempo limite (especialmente comuns em scripts de API de Monitor à escuta de dados). Para evitar tempos limite, ter o seu script enviar um ping com XMPP cada 10 minutos para manter a sessão alive com o servidor.

### <a name="see-also"></a>Consultar também
* [Documentação da API][Link 4]
* [Informações de protocolo com XMPP](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Não é possível utilizar a API para efetuar a mesma ação disponível na IU do Azure Mobile Engagement
### <a name="issue"></a>Problema
* Uma ação que funciona a partir da IU do Azure Mobile Engagement não funciona do relacionados API do Azure Mobile Engagement.

### <a name="causes"></a>Causas
* Confirmar que pode efetuar a mesma ação a partir da IU do Azure Mobile Engagement mostra que esta funcionalidade do Azure Mobile Engagement corretamente ter integrado com o SDK.

### <a name="see-also"></a>Consultar também
* [Documentação da IU][Link 1]

## <a name="error-messages"></a>Mensagens de erro
### <a name="issue"></a>Problema
* Códigos de erro utilizando a API apresentada no tempo de execução ou nos registos.

### <a name="causes"></a>Causas
* Eis uma lista composta de comuns números de códigos de estado de API de referência e a resolução de problemas preliminar:
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Consultar também
* [Documentação da API - para erros detalhados em cada API específica][Link 4]

## <a name="silent-failures"></a>Falhas automáticas
### <a name="issue"></a>Problema
* Ação de API falhar com nenhuma mensagem de erro apresentada no tempo de execução ou nos registos.

### <a name="causes"></a>Causas
* Número de itens vai ser desativado na IU do Azure Mobile Engagement se estes não são integrados corretamente, mas irá efetuar automaticamente a partir da API de, pelo que não se esqueça de testar a mesma funcionalidade da IU para ver se funciona.
* O Azure Mobile Engagement bem como muitas funcionalidades avançadas do Azure Mobile Engagement está a tentar utilizar, tem de ser individualmente integrado na aplicação com o SDK como passos separados antes de poder utilizar.

### <a name="see-also"></a>Consultar também
* [Resolução de problemas guia - SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

