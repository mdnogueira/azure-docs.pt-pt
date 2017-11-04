---
title: "Como recortar vídeos com o codificador de multimédia Standard - Azure | Microsoft Docs"
description: "Este artigo mostra como recortar vídeos com o codificador de multimédia Standard."
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 7628f674-2005-4531-8b61-d7a4f53e46ba
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2017
ms.author: anilmur;juliako;
ms.openlocfilehash: 60d0ce14a271fcbe698559da95ca011cb888b221
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="crop-videos-with-media-encoder-standard"></a>Recortar vídeos com o Media Encoder Standard
Pode utilizar suportes de dados codificador padrão (MES) para recortar vídeo a sua entrada. Cropping é o processo de selecionar um intervalo de retangular dentro da moldura de vídeo e codificação apenas os pixels esse período. O diagrama seguinte ajuda a ilustrar o processo.

![Recortar um vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponha que tem como entrada um vídeo que tem uma resolução de 1920 x 1080 pixels (proporção de 16:9), mas possui barras preta (pilar caixas) à esquerda e direita, para que apenas uma janela de 4:3 ou 1440 x 1080 pixels contém as vídeo Active Directory. Pode utilizar MES recortar ou editar a saída de barras preta e codificar a região 1440 x 1080.

Cropping no MES é uma fase de pré-processamento, pelo que os parâmetros cropping a predefinição de codificação aplicam para o vídeo de entrada original. Codificação é uma fase subsequente e aplicam as definições de largura/altura para a *pré-processados* vídeo e não para o vídeo original. Ao conceber a sua predefinição precisa de fazer o seguinte: (a) selecione os parâmetros de cortar com base no vídeo de entrada original e (b) selecione as definições com base no vídeo cropped de codificar. Se não corresponder ao seu definições para a vídeo cropped de codificar o resultado não, como esperado.

O [seguintes](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tópico mostra como criar uma tarefa de codificação com MES e como especificar um personalizado predefinido para a tarefa de codificação. 

## <a name="creating-a-custom-preset"></a>Criar uma predefinição personalizada
No exemplo mostrado no diagrama:

1. Entrada original é de 1920 x 1080
2. Tem de ser recortadas para uma saída de 1440 x 1080, que se centra-se na moldura de entrada
3. Isto significa que um desvio igual a X (1920 – 1440) / 2 = 240 e um Y de deslocamento de zero
4. A largura e altura do retângulo cortar são 1440 e 1080, respetivamente
5. Na fase codificar, a peça ao é para produzir três camadas, são resoluções 1440 x 1080, 960 x 720 e 480 x 360, respetivamente

### <a name="json-preset"></a>Predefinição JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


## <a name="restrictions-on-cropping"></a>Restrições na cropping
A funcionalidade cropping se destinar a ser manual. Terá de carregar o seu vídeo de entrada para uma ferramenta de edição adequada que lhe permite selecionar frames de interesse, posicione o cursor para determinar os desvios para o retângulo cropping, para determinar a predefinição de codificação está otimizada para esse etc de vídeo específico. Esta funcionalidade destina-se não ativar coisas como: deteção automática e a remoção de preto letterbox/pillarbox limites no seu vídeo de entrada.

As seguintes restrições aplicam-se à funcionalidade cropping. Se estes não são cumpridos, a codificar a tarefa pode falhar ou produzir um resultado inesperado.

1. O ordinates conjunta e o tamanho do retângulo cortar tem que caber no vídeo de entrada
2. Tal como mencionado acima, a largura e altura nas definições de codificar tem de corresponder ao vídeo cropped
3. Cropping aplica-se aos vídeos capturados no modo horizontal (ou seja, não é aplicável a vídeos registados com um smartphone contido verticalmente ou no modo de vertical)
4. Funciona melhor com as vídeo transferência progressiva capturada com quadrado pixels

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Consulte os Media Services do Azure percursos de aprendizagem para o ajudar a saber mais sobre excelentes funcionalidades oferecidas pelas AMS.  

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
