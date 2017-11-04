---
title: "Predefinições de tarefas para codificador de multimédia Standard (MES) | Microsoft Docs"
description: "O tópico proporciona e predefinições de descrição geral de exemplo definidas pelo serviço de suporte de dados codificador padrão (MES)."
author: Juliako
manager: cfow
editor: johndeu
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: juliako
ms.openlocfilehash: 5753b1dffe5a1a4ee069b83f58e9c2dac433b89d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Predefinições de exemplo para codificador de multimédia Standard (MES)

**Codificador de multimédia Standard** define um conjunto de predefinições, pode utilizar quando criar tarefas de codificação de codificação de sistema previamente definido. Recomenda-se para utilizar o "transmissão em fluxo adaptável" da configuração predefinida se pretende codificar um vídeo para transmissão em fluxo com os Media Services. Se especificar esta será codificador de multimédia Standard predefinida, [gerar automaticamente um ladder de velocidade de transmissão](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Criar predefinições personalizadas de amostras
Os Media Services suportam totalmente a personalizar todos os valores nas predefinições para satisfazer as suas necessidades específicas de codificação e requisitos. Se precisar de personalizar uma predefinição de codificação, deve começar com uma do abaixo predefinições do sistema que são fornecidas nesta secção como um modelo para a sua configuração personalizada. Para explicações de que cada elemento no meio estas predefinições e os valores válidos para cada elemento, consulte o [esquema codificador de multimédia Standard](media-services-mes-schema.md) tópico.  
  
> [!NOTE]
>  Quando utilizar uma predefinição de 4K codifica, pode ser obtido o `S3` reservados de um tipo de unidade. Para obter mais informações, consulte [como codificação de escala](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Predefinição rotação de vídeo no predefinições:
Ao trabalhar com o codificador de multimédia Standard, rotação vídeo está ativada por predefinição. Se as vídeo tem sido registado num dispositivo móvel no modo de vertical, em seguida, estas predefinições serão rodá-los para o modo horizontal antes da codificação.
 
## <a name="available-presets"></a>Predefinições disponíveis: 

 [H264 de múltipla 1080p áudio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produz um conjunto de 8 ficheiros MP4 alinhada GOP, vão de 6000 kbps 400 kbps e AAC 5.1 áudio.  
  
 [H264 múltipla 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) produz um conjunto de 8 ficheiros MP4 alinhada GOP, vão de 6000 kbps 400 kbps e stereo áudio AAC.  
  
 [H264 múltipla 16 x 9 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) produz um conjunto de 8 ficheiros MP4 alinhada GOP, vão de 8500 kbps 200 kbps e stereo áudio AAC.  
  
 [H264 múltipla 16 x 9 SD áudio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) produz um conjunto de 5 ficheiros MP4 alinhada GOP, vão de 1900 kbps 400 kbps e AAC 5.1 áudio.  
  
 [H264 múltipla 16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) produz um conjunto de 5 ficheiros MP4 alinhada GOP, vão de 1900 kbps 400 kbps e stereo áudio AAC.  
  
 [Áudio H264 múltipla 4K 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) produz um conjunto de 12 ficheiros MP4 alinhada GOP, vão de 20000 kbps a 1000 kbps e AAC 5.1 áudio.  
  
 [H264 múltipla 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) produz um conjunto de 12 ficheiros MP4 alinhada GOP, vão de 20000 kbps a 1000 kbps e stereo áudio AAC.  
  
 [H264 múltipla 4, 3 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) produz um conjunto de 8 ficheiros MP4 alinhada GOP, vão de 8500 kbps 200 kbps e stereo áudio AAC.  
  
 [H264 múltipla 4 x 3 SD áudio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) produz um conjunto de 5 ficheiros MP4 alinhada GOP, vão de 1600 kbps 400 kbps e AAC 5.1 áudio.  
  
 [H264 múltipla 4 x 3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) produz um conjunto de 5 ficheiros MP4 alinhada GOP, vão de 1600 kbps 400 kbps e stereo áudio AAC.  
  
 [H264 de múltipla 720p áudio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) produz um conjunto de 6 ficheiros MP4 alinhada GOP, vão de 3400 kbps 400 kbps e AAC 5.1 áudio.  
  
 [H264 múltipla 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) produz um conjunto de 6 ficheiros MP4 alinhada GOP, vão de 3400 kbps 400 kbps e stereo áudio AAC.  
  
 [Velocidade de transmissão única H264 1080p áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 6750 kbps e AAC 5.1 áudio.  
  
 [Velocidade de transmissão única do H264 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) produz um único ficheiro MP4 com velocidade de transmissão de 6750 kbps e stereo áudio AAC.  
  
 [Velocidade de transmissão única do H264 4K áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 18000 kbps e AAC 5.1 áudio.  
  
 [Velocidade de transmissão única do H264 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) produz um único ficheiro MP4 com velocidade de transmissão de 18000 kbps e stereo áudio AAC.  
  
 [Velocidade de transmissão única do H264 4 x 3 SD áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 1800 kbps e AAC 5.1 áudio.  
  
 [Velocidade de transmissão única do H264 4 x 3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) produz um único ficheiro MP4 com velocidade de transmissão de 1800 kbps e stereo áudio AAC.  
  
 [Velocidade de transmissão única do H264 16 x 9 SD áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 2200 kbps e AAC 5.1 áudio.  
  
 [Velocidade de transmissão única do H264 16 x 9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) produz um único ficheiro MP4 com velocidade de transmissão de 2200 kbps e stereo áudio AAC.  
  
 [Velocidade de transmissão única H264 720p áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) produz um único ficheiro MP4 com velocidade de transmissão de 4500 kbps e AAC 5.1 áudio.  
  
 [Velocidade de transmissão única do H264 720p para Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) predefinição produz um único ficheiro MP4 com velocidade de transmissão de 2000 kbps e stereo AAC.  
  
 [Velocidade de transmissão única do H264 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) produz um único ficheiro MP4 com velocidade de transmissão de 4500 kbps e stereo áudio AAC.  
  
 [H264 única de velocidade de transmissão elevada qualidade SD para Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produz um único ficheiro MP4 com velocidade de transmissão de 500 kbps e stereo áudio AAC...  
  
 [H264 única de velocidade de transmissão baixa qualidade SD para Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produz um único ficheiro MP4 com velocidade de transmissão de 56 kbps e stereo áudio AAC.  
  
 Para obter mais informações relacionadas com codificadores de Media Services, consulte [codificação a pedido com Media Services do Azure](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/).
