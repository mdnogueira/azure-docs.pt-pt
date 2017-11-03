---
title: "Guia de resolução de problemas para a transmissão em fluxo em direto | Microsoft Docs"
description: "Este tópico fornece sugestões sobre como resolver problemas de transmissão em fluxo em direto."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: fa91baf7c494941fccf0e6ca38b930f3c2a521ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guia de resolução de problemas de transmissão em fluxo em direto
Este tópico fornece sugestões sobre como resolver alguns problemas de transmissão em fluxo em direto.

## <a name="issues-related-to-on-premises-encoders"></a>Problemas relacionados com codificadores no local
Esta secção fornece sugestões sobre como resolver problemas relacionados com codificadores no local que estão configurados para enviar uma transmissão em fluxo para canais de AMS que estão ativados para live encoding.

### <a name="problem-would-like-to-see-logs"></a>Problema: Gostaria de ver registos
* **Potencial problema**: não é possível encontrar o codificador registos que podem ajudar a depurar problemas.
  
  * **Telestream Wirecast**: normalmente, pode encontrar registos em C:\Users\{username} \AppData\Roaming\Wirecast\ 
  * **Em direto elemental**: pode encontrar tem ligações para os registos no portal de gestão. Clique em **estatísticas**, em seguida, **registos**. No **ficheiros de registo** página, verá uma lista de registos de todos os itens LiveEvent; selecione aquele que correspondam a sua sessão atual. 
  * **Codificador em direto do suporte de dados de Flash**: pode encontrar o **diretório de registo...**  ao navegar até o **codificação registo** separador.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: Não há nenhuma opção para exportar um fluxo de transferência progressiva
* **Potencial problema**: O codificador que está a ser utilizado não deinterlace automaticamente. 
  
    **Passos de resolução de problemas**: procure uma opção Anular interlacing na interface do codificador. Depois de anular interlacing estiver ativada, verifique novamente para as definições de saída de transferência progressiva. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: Tentativa de várias definições de saída de codificador e ainda não é possível ligar.
* **Potencial problema**: não foi corretamente repor canal codificação do Azure. 
  
    **Passos de resolução de problemas**: Certifique-se o codificador já não é enviar para AMS, pare e repor o canal. Depois de executar de novo, tente ligar o codificador com as novas definições. Se esta ainda não corrigir o problema, tente criar um novo canal completamente, por vezes canais podem tornar-se danificada após várias tentativas falhadas.  
* **Potencial problema**: tamanho de GOP a ou as definições de fotograma chave não são otimizadas uma vez. 
  
    **Passos de resolução de problemas**: intervalo de tamanho ou keyframe GOP recomendada é 2 segundos. Alguns codificadores calcular esta definição no número de fotogramas, ao utilizam outros segundos. Por exemplo: quando exportar 30fps, o tamanho de GOP seria 60 frames, que é equivalente a 2 segundos.  
* **Potencial problema**: portas fechadas estão a bloquear o fluxo. 
  
    **Passos de resolução de problemas**: quando a transmissão em fluxo através de RTMP, verifique as definições de firewall e/ou de proxy para confirmar que as portas de saída 1935 e 1936 estão abertas. Quando utilizar RTP de transmissão em fluxo, confirme que a porta de saída 2010 está aberta. 

### <a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problema: Quando configurar o codificador de fluxo com o protocolo RTP, não há nenhuma local para introduzir um nome de anfitrião.
* **Potencial problema**: codificadores RTP muitos não permitem para nomes de anfitrião e um endereço IP, terá de ser adquiridas.  
  
    **Passos de resolução de problemas**: para localizar o endereço IP, abra uma linha de comandos em qualquer computador. Para fazê-lo no Windows, abra o iniciador de execução (WIN + R) e escreva "cmd" para abrir.  
  
    Depois de abrir a linha de comandos, escreva "Ping [nome do anfitrião do AMS]". 
  
    O nome de anfitrião pode ser derivado omitindo o número da porta do URL do Azure de inserção, conforme realçado no exemplo seguinte: 
  
    RTP://TEST2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 
  
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

> [!NOTE]
> Se depois de seguir os passos de resolução de problemas que ainda não é possível com êxito de fluxo, submeta um pedido de suporte no portal do Azure.
> 
> 

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

