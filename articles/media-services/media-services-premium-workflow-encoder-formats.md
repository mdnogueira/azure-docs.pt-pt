---
title: Formatos de fluxo de trabalho do suporte de dados codificador Premium e codecs | Microsoft Docs
description: "Este tópico fornece uma descrição geral dos formatos de formatos de fluxo de trabalho do suporte de dados codificador Premium e codecs"
services: media-services
documentationcenter: 
author: juliako
manager: erik43
editor: 
ms.assetid: b197fce8-3b9b-4189-8d08-486810c0426f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: e18de2adc9aac585d6890dd7b43a54f1a0ca177e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formatos de fluxo de trabalho do suporte de dados codificador Premium e codecs
> [!NOTE]
> Para perguntas de codificador premium, e-mail mepd em Microsoft.com.
> 
> Processador de multimédia de fluxo de trabalho do suporte de dados codificador Premium debatido neste tópico não está disponível na China. 
> 
> 

Este documento contém uma lista de formatos de ficheiro de entrada e de saída e codecs que são suportados pela versão de pré-visualização pública do **o fluxo de trabalho do suporte de dados codificador Premium** codificador.

[Suporte de dados codificador Premium Worflow entrada formatos e Codecs](#input_formats)

[Suporte de dados codificador Premium Worflow saída formatos e Codecs](#output_formats)

**O fluxo de trabalho do suporte de dados codificador Premium** suporta fechado captioning descrito [isto](#closed_captioning) secção. 

## <a id="input_formats"></a>O fluxo de trabalho do suporte de dados codificador Premium entrada formatos e Codecs
A secção seguinte apresenta uma lista de formatos de codecs e de ficheiros que suporte este processador de multimédia como entrada.

### <a name="input-containerfile-formats"></a>Formatos de contentor/ficheiro de entrada
* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Fluxos de transporte de MPEG-2
* Fluxos do programa de MPEG-2
* MPEG-4/MP4
* Suporte de dados/ASF do Windows
* AVI (descomprimido 8 bits/10 bit)

### <a name="input-video-codecs"></a>Codecs vídeo de entrada
* AVC 8 bits/10-bits, até 4:2:2, incluindo AVCIntra
* DNxHD avid (em MXF)
* DVCPro/DVCProHD (em MXF)
* JPEG2000
* MPEG-2 (até perfil 422 e de elevado nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Suporte de dados de Windows vídeo/VC-1

### <a name="input-audio-codecs"></a>Codecs entrada de áudio
* AES (SMPTE 331 M e 302 M, AES3 2003)
* Dolby® I
* Dolby® Digital (AC3)
* AAC (AAC LC, AAC Putador e AAC-HEv2; até 5.1)
* MPEG camada 2
* MP3 (MPEG-1 a camada áudio 3)
* Áudio de suporte de dados do Windows
* WAV/PCM

## <a id="output_format"></a>Formatos de saída de fluxo de trabalho do suporte de dados codificador Premium e Codecs
A secção seguinte apresenta uma lista de formatos de codecs e de ficheiros que são suportados como resultado deste processador de multimédia.

### <a name="output-containerfile-formats"></a>Formatos de contentor/ficheiro de saída
* Adobe® Flash® F4V
* MXF (OP1a, XDCAM e AS02)
* DPP (incluindo AS11)
* GXF
* MPEG-4/MP4
* Suporte de dados/ASF do Windows
* AVI (descomprimido 8 bits/10 bit)
* Formato de ficheiro transmissão em fluxo uniforme (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Saída Codecs vídeo
* AVC (264; 8 bits; até o perfil de alto nível 5.2; 4 HD Ultra estanques aos K; Dentro de AVC)
* DNxHD avid (em MXF)
* DVCPro/DVCProHD (em MXF)
* MPEG-2 (até perfil 422 e de elevado nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Suporte de dados de Windows vídeo/VC-1
* Criação de miniatura JPEG

### <a name="output-audio-codecs"></a>Saída de áudio Codecs
* AES (SMPTE 331 M e 302 M, AES3 2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (AC3-I) até 7.1
* AAC (AAC LC, AAC Putador e AAC-HEv2; até 5.1)
* MPEG camada 2
* MP3 (MPEG-1 a camada áudio 3)
* Áudio de suporte de dados do Windows

>[!NOTE]
>Se codificar para Dolby® digitais (AC3), a saída só pode ser escrita para um ficheiro ISO MP4.

## <a id="closed_captioning"></a>Suporte para Captioning fechada
De inserção, **o fluxo de trabalho do suporte de dados codificador Premium** suporta:

1. Ficheiros de SCC
2. Ficheiros de SMPTE-: s TT
3. CEA-608/CEA-708 – transportados como dados de utilizador (SEI as mensagens de fluxos básicas 264, ATSC/53, SCTE20) ou transportados como dados auxiliar nos ficheiros MXF/GXF
4. Ficheiros de subtitle STL

No resultado, as seguintes opções estão disponíveis:

1. CEA-608 para tradução de CEA 708
2. CEA 608/CEA 708 pass-through (incorporado nas mensagens SEI de fluxos de estatísticas básicas 264 ou transportados dados como auxiliar nos ficheiros MXF)
3. SCC
4. Texto de SMPTE excedeu o tempo limite (a partir da origem CEA 608 por SMPTE RP2052; incluindo DFXP criação de ficheiros)
5. Ficheiro de SRT Subtitle
6. Fluxos de subtitle DVB

Nota: não todas os formatos de saída acima são suportadas para a entrega por transmissão em fluxo numa Media Services do Azure.

## <a name="known-issues"></a>Problemas conhecidos
Se o seu vídeo de entrada não contém fechado captioning, a saída Asset ainda conterá um ficheiro TTML vazio. 

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

