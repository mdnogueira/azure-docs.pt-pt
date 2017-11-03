---
title: "Codificador de multimédia Standard formatos e codecs"
description: "Este tópico fornece uma descrição geral de codecs e de formatos de codificador de multimédia Standard."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: f334b1ce-2f56-4968-a019-f0a2b0016d9f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 1115408443e11c8b0d26b83217c5f63e4b6ba819
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Formatos e Codecs do Codificador de Multimédia Standard
Este documento contém uma lista de importação mais comuns e formatos de ficheiro de exportação que pode utilizar com o codificador de multimédia Standard.

## <a name="input-containerfile-formats"></a>Formatos de contentor/ficheiro de entrada
| Formatos de ficheiro (extensões de ficheiro) | Suportado |
| --- | --- | --- | --- |
| FLV (com codecs 264 e AAC) (.flv) |Sim |
| MXF (.mxf) |Sim |
| GXF (.gxf) |Sim |
| 3GP MPEG2-PS, MPEG2-TS (.ts .ps, .3gp, .3gpp, .mpg) |Sim |
| Vídeo de suporte de dados do Windows (WMV) / ASF (. wmv, .asf) |Sim |
| AVI (descomprimidos 8 bits/10 bits) (. avi) |Sim |
| MP4 (mp4 .m4a, .m4v) / ISMV (.isma, .ismv) |Sim |
| [Recording(DVR-MS) Vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr ms) |Sim |
| Matroska/WebM (.mkv) |Sim |
| WAVE/WAV (wav) |Sim |
| QuickTime (.mov) |Sim |

> [!NOTE]
> Acima é uma lista das extensões de ficheiro mais frequentemente foi encontrado. Codificador de multimédia Standard suporta muitas outras (por exemplo: .m2ts, .mpeg2video, .qt). Se tentar codificar um ficheiro e receber uma mensagem de erro sobre o formato não suportado, forneça um comentários [aqui](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formatos de áudio em contentores de entrada
Codificador de multimédia Standard suporta as devidas os seguintes formatos de áudio em contentores de entrada:

* MXF, GXF e QuickTime ficheiros que tenham controla áudio com stereo intercalado ou 5.1 amostras

ou

* Ficheiros MXF, GXF e QuickTime onde áudio é executado como separada PCM controla mas o canal mapeamento (stereo ou 5.1) pode ser deduzido dos metadados de ficheiro

Tenha em atenção que suportam para mapeamento de canal explícita/fornecido pelo utilizador irá ser fornecido num futuro próximo.

## <a name="input-video-codecs"></a>Codecs vídeo de entrada
| Codecs vídeo de entrada | Suportado |
| --- | --- | --- | --- |
| AVC 8 bits/10-bits, até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |
| DNxHD avid (em MXF) |Sim |
| DVCPro/DVCProHD (em MXF) |Sim |
| Vídeo digital (DV) (em ficheiros AVI) |Sim |
| JPEG 2000 |Sim |
| MPEG-2 (até perfil 422 e de elevado nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Perfil até 422 |
| MPEG-1 |Sim |
| VC-1/WMV9 |Sim |
| Canopus HQ/HQX |Não |
| MPEG-4 parte 2 |Sim |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |
| YUV420 descomprimidos ou mezanino |Sim |
| Apple ProRes 422 |Sim |
| Apple ProRes 422 LT |Sim |
| Apple ProRes 422 HQ |Sim |
| Apple ProRes Proxy |Sim |
| Apple ProRes 4444 |Sim |
| Apple ProRes 4444 XQ |Sim |

## <a name="input-audio-codecs"></a>Codecs entrada de áudio
| Codecs entrada de áudio | Suportado |
| --- | --- | --- | --- |
| AAC (AAC LC, AAC Putador e AAC-HEv2; até 5.1) |Sim |
| MPEG camada 2 |Sim |
| MP3 (MPEG-1 a camada áudio 3) |Sim |
| Áudio de suporte de dados do Windows |Sim |
| WAV/PCM |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |
| [Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |Sim |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |
| AMR (taxa multi adaptável) |Sim |
| AES (SMPTE 331 M e 302 M, AES3 2003) |Não |
| Dolby® I |Não |
| Dolby® Digital (AC3) |Não |
| Dolby® Digital Plus (AC3-I) |Não |

## <a name="output-formats-and-codecs"></a>Formatos de saída e codecs
A tabela seguinte lista os formatos de codecs e de ficheiros que são suportados para exportação.

| Formato de ficheiro | Codec de vídeo | Codec de áudio |
| --- | --- | --- |
| MP4 <br/><br/>(incluindo contentores de MP4 de velocidade de transmissão múltipla) |264 (alta, principal e os perfis de linha de base) |AAC-LC, AAC Putador v1, v2 Putador AAC |
| MPEG2 TS |264 (alta, principal e os perfis de linha de base) |AAC-LC, AAC Putador v1, v2 Putador AAC |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar também
[A codificação de conteúdo a pedido com os Media Services do Azure](media-services-encode-asset.md)

[Como codificar com o codificador de multimédia Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

