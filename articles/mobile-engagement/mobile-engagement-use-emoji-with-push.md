---
title: Utilizar Emoji emoticons dentro do Azure Mobile Engagement
description: "Como utilizar Emoji emoticons dentro as notificações push"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bbb7ce5e95b229a7505c5e97b6866d5a302a1d27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-emoji-emoticon-within-push-notifications"></a>Utilizar Emoji emoticon dentro de notificações Push
Pode incluir Emoji emoticons no seu notificação push no alguns passos simples: 

1. Primeiro de tudo o que precisa para localizar o Emoji que pretende enviar a mensagem. Certifique-se de que o Emoji selecionar será suportado pelo dispositivo de destino como dispositivo manufactures demorar algum tempo para adicionar Emojis recentemente aprovados para as plataformas de dispositivos. 
2. No **Windows** -pode navegar para esta [ligação](http://apps.timwhitlock.info/emoji/tables/unicode) e copie o ícone 'Native'.
   
    ![][7] 
3. No **Mac** -pode encontrar Emojis na aplicação de dicionário em Editar -> Emoji & símbolos.
   
    ![][6] 
4. Agora, vá para o **alcançar** separador no portal do Azure Mobile Engagement. Selecione o tipo da sua notificação push (anúncio, consulta etc). Para este exemplo, escolha um push de anúncio.
5. Especifique os campos diferentes da notificação até chegar o texto da notificação. Este é onde adicionará o Emoji Emoticon. Pode optar por coloque-o título, a mensagem ou ambos. Terá de arrastar e largar ou copie o Emoji localizar a partir de localizações acima. 
   
    ![][1]
6. Conclua os outros campos para a notificação e guardá-lo. 
7. Quando executar um teste ou ativar o anúncio, verá uma notificação com o emoticon conforme especificado.   
   
    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

