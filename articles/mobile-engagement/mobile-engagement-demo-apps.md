---
title: "Aplicação de demonstração do Azure Mobile Engagement | Microsoft Docs"
description: "Descreve onde pode transferir, como utilizar e as vantagens de utilizar a aplicação de demonstração do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: 8381edb569e19a85c1259f7791b477cfa6e51ea3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-demo-app"></a>Aplicação de demonstração do Azure Mobile Engagement
Iremos tiver publicado uma aplicação de demonstração do Azure Mobile Engagement para **iOS**, **Android**, e **Windows** plataformas para ajudar a localizar recursos úteis e saber mais sobre o Mobile Engagement.

A aplicação ajuda-o a:

* Localize facilmente útil ligações para recursos de Mobile Engagement como vídeos, documentação, o Fórum de suporte e onde ir para emitir pedidos de funcionalidades.
* Experiência de notificações de exemplo que são suportadas pelo Mobile Engagement para obter ideias para as suas próprias aplicações móveis.
* Utilize uma implementação de referência para estudar a como implementar o Mobile Engagement para a própria aplicação. Pode aprender a:
  
  * Recolha dados de análise.
  * Implementar avançada, como cenários de notificação dos tipos *interstitial de ecrã inteiro* ou *pop-up*.
  * Implementar inquéritos e inquéritos.
  * Implemente cenários de push de dados e de emissão automática.   

## <a name="app-installation"></a>Instalação da aplicação
Esta aplicação está disponível nos arquivos de aplicação seguinte:

* **Aplicação de demonstração Windows Universal**:
  
  * Transferir a aplicação no [loja de aplicações do Windows](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * A aplicação foi desenvolvida como uma aplicação Universal do Windows 10. O código de origem está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **aplicação de demonstração de iOS**:
  
  * Transferir a aplicação no [Apple store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * A aplicação foi desenvolvida em iOS Swift. O código de origem está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **Aplicação de demonstração Android**:
  
  * Transferir a aplicação no [loja do Google Play](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * O código de origem está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Aplicação de demonstração Windows Universal][1]

![aplicação de demonstração de iOS][2]
![aplicação de demonstração de Android][3]

## <a name="usage"></a>Utilização
Pode utilizar esta aplicação das seguintes formas:

**Transferir a aplicação no seu dispositivo a partir de ligações de arquivo de aplicação (fornecidas anteriormente):**

> [!IMPORTANT]
> Não precisa de uma conta do Azure ou a necessidade de se ligar a aplicação para um back-end. A aplicação funciona de forma independente.
> 
> 

* Depois de ter a aplicação no seu dispositivo, em seguida, pode percorrer as ligações no menu da esquerda para encontrar os recursos úteis sobre Mobile Engagement.
* Adicionámos o [feed RSS do serviço](https://aka.ms/azmerssfeed) para esta aplicação para que o está sempre atualizado sobre as atualizações mais recentes do produto.
* Também pode percorrer os cenários de notificação de exemplo para experimentar o tipo de notificações que são suportadas pelo Mobile Engagement para cada plataforma. Estas notificações podem ser teve localmente – que é, pode clicar em botões em ecrãs para lhe mostrar a experiência de notificações, que é idêntica ao envio de notificações da plataforma Mobile Engagement.

![Menu de aplicação para Windows][4]

![Menu de aplicação para iOS][5]
![menu de aplicação para Android][6]

**Transferir o código de origem a partir de ligações de GitHub (fornecidas anteriormente):**

* Depois de transferir o código de origem, abra-a no ambiente de desenvolvimento respetivos – XCode para iOS, Android Studio para Android e o Visual Studio para Windows.
* Deve seguir junto nosso [passos de integração do SDK básicos](mobile-engagement-windows-store-dotnet-get-started.md) , de modo a que poderá ligar esta aplicação a sua própria instância de back-end do Mobile Engagement.
  * Terá de configurar uma cadeia de ligação na aplicação.
  * Terá também de configurar a plataforma de notificação push para a sua aplicação.
* Irá reparar que a aplicação em si é instrumentada Mobile engagement. Por conseguinte, como abrir a aplicação depois de ligar ao back-end, poderá ver a sessão do utilizador, atividades, eventos e assim sucessivamente, no **Monitor** separador.
* Também poderá enviar notificações para esta aplicação a partir da sua própria instância do Mobile Engagement, em vez de utilizar notificações locais.
  
  * Aqui pode adicionar o seu dispositivo como um dispositivo de teste utilizando o **obter o ID de dispositivo** item de menu na aplicação. Isto dá-lhe um ID de dispositivo que, em seguida, registados como um dispositivo de teste com a instância de back-end de plataforma.
    
    ![ID de dispositivo no Windows][7]
    
    ![ID do dispositivo IOS][8]
    ![ID de dispositivo no Android][9]

## <a name="key-features-of-the-demo-app"></a>Principais funcionalidades da aplicação de demonstração
* Conforme mencionado anteriormente, com esta aplicação, terá todos os recursos de chaves para Mobile Engagement na mão. Pode percorrer as ligações no menu da esquerda.
* Pode deparar-notificações de fora da aplicação para cada plataforma. Estas notificações podem ser fornecidas como **apenas notificação**, onde clicando a notificação simplesmente abre-se um ecrã nativo da aplicação (através da utilização de **ligar profunda**) – ou como um **anúncio Web**, onde pode fornecer conteúdo HTML adicional do Mobile Engagement back-end que será apresentado quando a notificação é clicada.
  
    ![Notificações de fora da aplicação][29]
* No iOS, tem de fechar a aplicação para ver as notificações de push fora da aplicação ou sistema. Pode ver a implementação para adicionar **botões de ação**, como aqueles que são adicionados a esta notificação de fora da aplicação para *comentários* e *partilha* (de modo a que o utilizador pode agir diretamente a partir da notificação do próprio).
  
    ![Notificações de fora da aplicação em dispositivos iOS][11] ![Apresentação de notificações de fora da aplicação em dispositivos iOS][14]
* No Android, as opções suportadas são adicionar texto com várias linhas (**texto grande**) ou uma imagem de notificação (**visão**) para a notificação, juntamente com o **botões de ação** (como suportados pelo iOS).
  
    ![Notificações de fora da aplicação no Android][12] ![Apresentação de notificações de fora da aplicação no Android][15]
* No Windows 10, pode ver o aspeto de notificações no PC. Esta notificação também aparece no Windows 10 **notificação Center**. Não são suportadas para adicionar **botões de ação** de momento, o SDK do Windows.
  
    ![Notificações de fora da aplicação no Windows][10] ![Apresentação de fora da aplicação no Windows][13]
* Pode deparar-"predefinição nas notificações na aplicação" para cada plataforma. Esta é uma experiência de dois passos em que um **notificação** janela é apresentada primeiro. Quando clica, abre um ecrã inteiro **anúncio**, como apresentado na seguinte captura de ecrã. O conteúdo deste anúncio for proveniente da sua instância de back-end do Mobile Engagement. O SDK tem os modelos para notificações e anúncios. Facilmente pode personalizá-las, conforme mostrado nesta aplicação de demonstração com a adição dos nossos logótipo e a coloração.  
  
    ![Nas notificações na aplicação no Windows][16]
  
    ![Nas notificações na aplicação em dispositivos iOS][17]  ![Nas notificações na aplicação no Android][18]
  
    **iOS**, **Android**
* Também pode utilizar o **categoria** funcionalidade do Mobile Engagement para personalizar esta experiência de predefinição. Na aplicação de demonstração, vamos tiver demonstrado duas formas comuns de alterar a experiência das notificações. Tenha em atenção que a funcionalidade de categoria ainda não é suportada no Windows SDK.
  
    **Ecrã inteiro interstitial:**
  
    ![Notificação na aplicação - Interstitial categoria][30]
  
    ![Categoria Interstitial no iOS][21]     ![Categoria Interstitial no Android][22]
  
    **Notificação de pop-up:**
  
    ![Notificação na aplicação - categoria pop-up][31]
  
    ![Notificação de pop-up no iOS][19]    ![Notificação de pop-up no Android][20]

**iOS**, **Android**

* O Mobile Engagement também suporta um tipo especializado de notificação na aplicação chamado **inquéritos**. Isto permite-lhe enviar inquéritos rápidos aos seus utilizadores segmentados de aplicação. Pode adicionar perguntas e as opções para cada pergunta como no seguinte captura de ecrã. Em seguida, irá obter apresentada como uma notificação na aplicação para o utilizador de aplicação.   
  
    ![Notificações de consulta][32]
  
    ![Inquérito no Windows][26]
  
    ![Encontrar no iOS][27]   ![Inquérito no Android][28]

**iOS**, **Android**

* O Mobile Engagement também suporta o envio de automática **Push de dados** notificações. Com estas notificações, pode enviar dados entre o serviço (por exemplo, os dados JSON no exemplo seguinte), que pode processar na sua aplicação e efetuar qualquer ação. Um exemplo é como podemos estiver a alterar o preço de um item seletivamente por meio de notificações de push de dados.
  
    ![Notificação push de dados][33]
  
    ![Notificação push de dados no Windows][23]
  
    ![Notificação push de dados no iOS][24]  ![Notificação push de dados no Android][25]

**iOS**, **Android**

> [!NOTE]
> Pode ver instruções passo a passo detalhadas para qualquer um destas notificações clicando **clique aqui para obter instruções sobre como enviar essas notificações da plataforma Mobile Engagement** em qualquer ecrã de notificação de exemplo.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
