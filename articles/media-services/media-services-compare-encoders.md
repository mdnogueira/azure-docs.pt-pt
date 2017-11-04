---
title: "Comparação do Azure em codificadores de suporte de dados a pedido | Microsoft Docs"
description: "Este tópico compara as capacidades de codificação do * * multimédia codificador padrão * * e * * multimédia codificador Premium fluxo de trabalho * *."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: 3bba48d5425ae47c72aca893af7c11fe270c2929
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparação do Azure em codificadores de suporte de dados a pedido

Este tópico compara as capacidades de codificação do **codificador de multimédia Standard** e **o fluxo de trabalho do suporte de dados codificador Premium**.

## <a name="video-and-audio-processing-capabilities"></a>Capacidades de processamento de áudio e vídeo

A tabela seguinte compara as funcionalidades entre o suporte de dados codificador padrão (MES) e o suporte de dados codificador Premium fluxo de trabalho (MEPW). 

|Capacidade|Media Encoder Standard|Fluxo de Trabalho Premium de Codificador de Multimédia|
|---|---|---|
|Aplicar a lógica condicional ao codificação<br/>(por exemplo, se a entrada é HD, em seguida, codificar áudio 5.1)|Não|Sim|
|Fechado captioning|Não|[Sim](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Correção do Dolby® Loudness profissionais](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> com o diálogo Intelligence™|Não|Sim|
|Telecine anular interlacing, inverso|Básica|Qualidade de difusão|
|Detetar e remover limites preto <br/>(pillarboxes, letterboxes)|Não|Sim|
|Geração em miniatura|[Sim](media-services-dotnet-generate-thumbnail-with-mes.md)|[Sim](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Recorte/corte e stitching dos vídeos do|[Sim](media-services-advanced-encoding-with-mes.md#trim_video)|Sim|
|Sobreposição de vídeo ou de áudio|[Sim](media-services-advanced-encoding-with-mes.md#overlay)|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Sobreposição de gráficos|A partir de origens de imagem|A partir de origens de texto e imagem|
|Vários idiomas áudio controla|Limitado|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Utilizado por cada codificador de medição de faturação
| Nome do processador de suporte de dados | Obter preços aplicáveis | Notas |
| --- | --- | --- |
| **Media Encoder Standard** |CODIFICADOR |Codificação tarefas serão cobradas com base na duração total, em minutos, de todos os ficheiros de suporte de dados produzidos como resultado, a taxa especificado [aqui][1], sob a coluna de CODIFICADOR. |
| **Fluxo de Trabalho Premium do Codificador de Multimédia** |CODIFICADOR PREMIUM |Codificação tarefas serão cobradas com base na duração total, em minutos, de todos os ficheiros de suporte de dados produzidos como resultado, a taxa especificado [aqui][1], sob a coluna de CODIFICADOR PREMIUM. |

## <a name="input-containerfile-formats"></a>Formatos de contentor/ficheiro de entrada
| Formatos de contentor/ficheiro de entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Sim |Sim |
| MXF/SMPTE 377M |Sim |Sim |
| GXF |Sim |Sim |
| Fluxos de transporte de MPEG-2 |Sim |Sim |
| Fluxos do programa de MPEG-2 |Sim |Sim |
| MPEG-4/MP4 |Sim |Sim |
| Suporte de dados/ASF do Windows |Sim |Sim |
| AVI (descomprimido 8 bits/10 bit) |Sim |Sim |
| 3GPP/3GPP2 |Sim |Não |
| Formato de ficheiro transmissão em fluxo uniforme (PIFF 1.3) |Sim |Não |
| [Recording(DVR-MS) Vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Sim |Não |
| Matroska/WebM |Sim |Não |
| QuickTime (.mov) |Sim |Não |

## <a name="input-video-codecs"></a>Codecs vídeo de entrada
| Codecs vídeo de entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC 8 bits/10-bits, até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |Sim |
| DNxHD avid (em MXF) |Sim |Sim |
| DVCPro/DVCProHD (em MXF) |Sim |Sim |
| JPEG2000 |Sim |Sim |
| MPEG-2 (até perfil 422 e de elevado nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Perfil até 422 |Sim |
| MPEG-1 |Sim |Sim |
| Suporte de dados de Windows vídeo/VC-1 |Sim |Sim |
| Canopus HQ/HQX |Não |Não |
| MPEG-4 parte 2 |Sim |Não |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |Não |
| Apple ProRes 422 |Sim |Não |
| Apple ProRes 422 LT |Sim |Não |
| Apple ProRes 422 HQ |Sim |Não |
| Apple ProRes Proxy |Sim |Não |
| Apple ProRes 4444 |Sim |Não |
| Apple ProRes 4444 XQ |Sim |Não |

## <a name="input-audio-codecs"></a>Codecs entrada de áudio
| Codecs entrada de áudio | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331 M e 302 M, AES3 2003) |Não |Sim |
| Dolby® I |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (AC3-I) |Não |Sim |
| AAC (AAC LC, AAC Putador e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG camada 2 |Sim |Sim |
| MP3 (MPEG-1 a camada áudio 3) |Sim |Sim |
| Áudio de suporte de dados do Windows |Sim |Sim |
| WAV/PCM |Sim |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |Não |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Sim |Não |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |Não |

## <a name="output-containerfile-formats"></a>Formatos de contentor/ficheiro de saída
| Formatos de contentor/ficheiro de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Não |Sim |
| MXF (OP1a, XDCAM e AS02) |Não |Sim |
| DPP (incluindo AS11) |Não |Sim |
| GXF |Não |Sim |
| MPEG-4/MP4 |Sim |Sim |
| MPEG-TS |Sim |Sim |
| Suporte de dados/ASF do Windows |Não |Sim |
| AVI (descomprimido 8 bits/10 bit) |Não |Sim |
| Formato de ficheiro transmissão em fluxo uniforme (PIFF 1.3) |Não |Sim |

## <a name="output-video-codecs"></a>Saída codecs vídeo
| Saída Codecs vídeo | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC (264; 8 bits; até o perfil de alto nível 5.2; 4 HD Ultra estanques aos K; Dentro de AVC) |Apenas 8 bits 4:2:0 |Sim |
| DNxHD avid (em MXF) |Não |Sim |
| MPEG-2 (até perfil 422 e de elevado nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Não |Sim |
| MPEG-1 |Não |Sim |
| Suporte de dados de Windows vídeo/VC-1 |Não |Sim |
| Criação de miniatura JPEG |Sim |Sim |
| Criação de miniatura PNG |Sim |Sim |
| Criação de miniatura de BMP |Sim |Não |

## <a name="output-audio-codecs"></a>Saída de áudio codecs
| Saída de áudio Codecs | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331 M e 302 M, AES3 2003) |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (AC3-I) até 7.1 |Não |Sim |
| AAC (AAC LC, AAC Putador e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG camada 2 |Não |Sim |
| MP3 (MPEG-1 a camada áudio 3) |Não |Sim |
| Áudio de suporte de dados do Windows |Não |Sim |

>[!NOTE]
>Se codificar para Dolby® digitais (AC3), a saída só pode ser escrita para um ficheiro ISO MP4.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas de codificação avançadas ao personalizar predefinições codificador de multimédia Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
