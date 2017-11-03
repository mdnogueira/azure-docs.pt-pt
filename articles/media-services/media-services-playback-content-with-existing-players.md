---
title: "Utilizar jogadores existentes para reproduzir o conteúdo - Azure | Microsoft Docs"
description: "Este tópico lista jogadores existentes que pode utilizar para reproduzir o conteúdo."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
ms.openlocfilehash: 48f373b013b1192c353352b801876d706d91dd28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="playing-your-content-with-existing-players"></a>Reprodução do seu conteúdo com jogadores existentes
Media Services do Azure suporta vários formatos de transmissão em fluxo populares, tais como a transmissão em fluxo uniforme, para transmissão em fluxo em direto HTTP e MPEG-Dash. Este tópico indica jogadores existentes que pode utilizar para testar os fluxos.

### <a name="the-azure-portal-media-services-content-player"></a>O Azure portal dos Media Services leitor de conteúdos
O **Azure** portal fornece um leitor de conteúdos que pode utilizar para testar o vídeo.

Clique no vídeo pretendido (Certifique-se de que foi [publicados](media-services-portal-publish.md)) e clique em de **reproduzir** na parte inferior do portal.

São aplicáveis algumas considerações:

* O **LEITOR DE CONTEÚDOS DE MEDIA SERVICES** reproduz a partir do ponto final de transmissão em fluxo predefinido. Se pretender reproduzir a partir de um ponto final de transmissão em fluxo não predefinido, utilize outro leitor. Por exemplo, [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Media Player do Azure
Utilize [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reproduzir o conteúdo (encriptado ou protegido) em qualquer um dos seguintes formatos:

* Transmissão em Fluxo Uniforme
* MPEG DASH
* HLS
* MP4 transferência progressiva

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>Encriptação AES com Token
[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Silverlight jogadores
#### <a name="monitoring"></a>Monitorização
[http://SMF.cloudapp.NET/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>PlayReady com Token
[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>TRAÇO jogadores
[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Outros
Para testar os URLs de HLS também pode utilizar:

* **Safari** num dispositivo iOS ou
* **Leitor HLS 3ivx** no Windows.

## <a name="developing-video-players"></a>Desenvolver as vídeos jogadores
Para obter informações sobre como desenvolver as suas próprias jogadores, consulte [desenvolver as vídeos jogadores](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
