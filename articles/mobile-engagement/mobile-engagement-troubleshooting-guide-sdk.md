---
title: "Do Azure Mobile Engagement SDK - Guia de resolução de problemas"
description: "Resolução de problemas de integração do SDK no Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4d9d6165deb4bd0c65f1841aa7c457363a1f2865
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Guia de resolução de problemas para problemas de integração do SDK
Seguem-se possíveis problemas que pode surgir com o Azure Mobile Engagement integra-se na sua aplicação.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problemas SDK detetados por uma falha na outra área da sua aplicação
### <a name="issue"></a>Problema
* Falha de recolha de dados de IU (de análise, monitorização, segmentação ou Dashboards).
* Push falhas (Pushes não funcionam na aplicação, fora da aplicação, ou ambos).
* Avançada funcionalidade falhas (controlo, Geolocalização ou plataforma que pushes específicos não funcionam).
* Falhas de API (APIs falhar, muitas vezes, silenciosamente sem mensagens de erro).
* Falhas de serviço (nenhum dos Azure Mobile Engagement funciona para a sua aplicação).

### <a name="causes"></a>Causas
* A maioria dos problemas que necessitam de ser resolvido com o Azure Mobile Engagement SDK serão detetados por uma falha na sua aplicação (por exemplo, uma falha de recolha de dados de IU, push falha, falha de funcionalidade avançada, falha de API, falhas de aplicação ou interrupção do serviço aparente) .  
* Se um determinado recurso do Azure Mobile Engagement nunca trabalhou na sua aplicação antes, terá de concluir a integração. 
* Se um determinado recurso do Azure Mobile Engagement estava a funcionar e parado, poderá ter de atualizar para a última versão com o Azure Mobile Engagement SDK. Lembre-se de que não há uma versão diferente do Azure Mobile Engagement SDK para cada plataforma suportada pelo Azure Mobile Engagement (Android, iOS, Windows e Windows Phone).

#### <a name="sdk-integration"></a>Integração SDK
* O Azure Mobile Engagement correctamente integrada no SDK (análise).
* Alcançar correctamente integrada no SDK (na aplicação e fora de Pushes aplicação).
* Certificado vs de PROD expiradas ou incorretas. Programador (apenas iOS).
* GCM ou ADM não corretamente integrada no SDK (Android só - serviço específico Pushes).
* Controlo correctamente integrada no SDK (arquivo de instalação de controlo).
* Em diferido localização ou localização de GPS correctamente integrada no SDK (filtragem por geolocalização).

**Consulte também:**

* [Documentação do SDK - guias de integração][Link 5] 
* [Resolução de problemas guia - Push][Link 23]

#### <a name="sdk-upgrade"></a>Atualização do SDK
* Tem de atualizar o SDK para resolver problemas com versões anteriores do SDK (frequentemente relacionados com versões mais recentes do SO do dispositivo).
* Desinstale todas as versões anteriores da sua aplicação do seu dispositivo e reinstale a versão mais recente da sua aplicação, a voltar a registar o ID do dispositivo do Azure Mobile Engagement da IU para confirmar que o seu dispositivo está a utilizar a versão mais recente da sua aplicação.

**Consulte também:**

* [Documentação do SDK - notas de versão](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [Documentação do SDK - guias de atualização](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>SDK outro
* Erros no Application Manifest "AndroidManifest.xml" podem fazer com que o Azure Mobile Engagement não funcionam (apenas Android).
* Um problema comum verificado com a integração do SDK e a utilização da API é confunda a chave do SDK e a chave de API.

**Consulte também:**

* [Conceitos - glossário][Link 6]

## <a name="advanced-coding-issues"></a>Avançadas codificação problemas
### <a name="issue"></a>Problema
* Código de plataforma específico não diretamente relacionadas com o Azure Mobile Engagement pode causar problemas no iOS, Android e Windows Phone.

### <a name="causes"></a>Causas
* Muitas avançadas codificação problemas com o Azure Mobile Engagement são causadas por código específico de plataforma mal escrito não diretamente relacionadas com o Azure Mobile Engagement. Terá de consulte a documentação específica da plataforma que estiver a desenvolver para além de documentação do Azure Mobile Engagement (Android, iOS, Web, Windows e Windows Phone).
* Configurar correctamente "categorias", impede a ligar a partir de uma notificação para outra localização dentro ou fora da aplicação (apenas Android). 
* Não definir "UIKit.framework" para "opcional" no seu código de iOS, mostra um "Símbolo não foi encontrado o erro" e/ou falhas em dispositivos iOS mais antigos (apenas iOS).
* Certificados expirados ou não corretamente utilizando a versão de certificado, causas DEV ou Prod push emite (apenas iOS).
* Existem algumas limitações inerente para uma plataforma que não é possível controlar o Azure Mobile Engagement (como a forma como o system center funciona para fora da aplicação pushes em Android e iOS).
* O Azure Mobile Engagement publica uma lista completa dos pacotes internos utilizado pelo Azure Mobile Engagement para iOS e Android para referência. Tenha em atenção que algumas funcionalidades do Azure Mobile Engagement são específicas da plataforma (Android, iOS, Web, Windows e Windows Phone).

### <a name="see-also"></a>Consultar também
* [Resolução de problemas guia - Push][Link 23] 
* [Documentação do SDK - notas de versão][Link 5]
* [Documentação do SDK - guias de atualização][Link 5]

## <a name="application-crashes"></a>Falhas na aplicação
### <a name="issue"></a>Problema
* Falha da sua aplicação no dispositivo dos utilizadores finais.

### <a name="causes"></a>Causas
* Informações da falha podem ser visualizadas no *IU de análise* ou *API de análise*
* Pode encontrar o ID de dispositivo do seu dispositivo de teste e ter a mesma ação que causou a falha da aplicação para um utilizador final ajudar a identificar a causa da falha.
* Problemas conhecidos com o Azure Mobile Engagement SDK que fazer com que as aplicações de falhas por vezes, são resolvidos através da atualização para a versão mais recente do SDK. Certifique-se verificar as notas de versão sobre a sua plataforma quando investigar falhas.

### <a name="see-also"></a>Consultar também
* [Documentação do SDK - notas de versão][Link 5]
* [Documentação do SDK - guias de atualização][Link 5]

## <a name="app-store-upload-failures"></a>Falhas de carregamento do arquivo de aplicação
### <a name="issue"></a>Problema
* Erros relacionados com a carregar a versão mais recente da sua aplicação para Apple, Google ou da loja de aplicações do Windows.

### <a name="causes"></a>Causas
* Aplicação, por vezes, armazena impedir que as aplicações com determinadas funcionalidades ativadas (por exemplo, a Apple Store impede a utilização de IDFV em aplicações na loja e o arquivo de GooglePlay impede que a partilha de informações sobre a aplicação entre aplicações). 
* Certifique-se de que seleciona as notas de versão sobre a plataforma e a versão atual do SDK se tiver dificuldade em carregar uma aplicação para o arquivo.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
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

