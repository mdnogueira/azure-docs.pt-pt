---
title: "Design híbrido do subsystem(s) DRM utilizando os Media Services do Azure | Microsoft Docs"
description: "Este tópico descreve o design híbrido do subsystem(s) DRM utilizando os Media Services do Azure."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 841b1164db6fd1a2c029b98392509c15f23158e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="hybrid-design-of-drm-subsystems"></a>Design híbrido do DRM subsystem(s)

Este tópico descreve o design híbrido do subsystem(s) DRM utilizando os Media Services do Azure.

## <a name="overview"></a>Descrição geral

Media Services do Azure fornece suporte para o sistema DRM três seguintes:

* PlayReady
* Widevine (modulares)
* FairPlay

O suporte DRM inclui encriptação DRM (encriptação dinâmica) e entrega de licença, com o Azure Media Player suportar todos os 3 DRMs como um leitor de browser SDK.

Para um tratamento detalhado de DRM/CENC subsistema design e implementação, consulte o documento intitulado [CENC com múltipla DRM e controlo de acesso](media-services-cenc-with-multidrm-access-control.md).

Embora que oferecem suporte completo para três sistemas DRM, por vezes, os clientes tem de utilizar várias partes da suas própria infraestrutura subsistemas para além dos Media Services do Azure para criar uma versão híbrida subsistema DRM.

Abaixo são algumas perguntas comuns pedidas por clientes:

* "Posso utilizar o meus próprio servidores de licença DRM?" (Neste caso, os clientes tenham investido num farm de servidores de licença DRM com a lógica de negócio incorporados).
* "Posso utilizar apenas a entrega de licença DRM nos Media Services do Azure sem que aloja o conteúdo do AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularidade da plataforma de AMS DRM

Como parte de uma plataforma de vídeo de nuvem abrangente, DRM de serviços de suporte de dados do Azure tem uma estrutura com a flexibilidade e modularidade em mente. Pode utilizar os Media Services do Azure com qualquer uma das seguintes combinações de diferentes descritas na tabela abaixo (obter uma explicação sobre a notação utilizada na tabela seguinte). 

|**Alojamento de & origem de conteúdo**|**Encriptação de conteúdo**|**Entrega de licença DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Terceiros|
|AMS|Terceiros|AMS|
|AMS|Terceiros|Terceiros|
|Terceiros|Terceiros|AMS|

### <a name="content-hosting--origin"></a>Alojamento de & origem de conteúdo

* AMS: elemento de vídeo está alojado AMS e de transmissão em fluxo é efetuada através de pontos finais de transmissão em fluxo de AMS (mas empacotamento dinâmico, não necessariamente).
* Terceiros: as vídeo está alojada e entregues a uma plataforma de transmissão em fluxo de terceiros fora do AMS.

### <a name="content-encryption"></a>Encriptação de conteúdo

* AMS: encriptação de conteúdo é efetuada dinamicamente/a pedido através da encriptação dinâmica de AMS.
* Terceiros: encriptação de conteúdo é executada fora do AMS utilizando um fluxo de trabalho de processamento previamente.

### <a name="drm-license-delivery"></a>Entrega de licença de DRM

* AMS: Licença DRM é entregue ao serviço de entrega de licença do AMS.
* Terceiros: Licença DRM é entregue por um servidor de licença DRM terceiros fora do AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configurar com base no seu cenário híbrido

### <a name="content-key"></a>Chave de conteúdo

Através da configuração de uma chave de conteúdo, pode controlar os seguintes atributos de encriptação dinâmica AMS e o serviço de entrega de licença do AMS:

* A chave de conteúdo utilizada para encriptação DRM dinâmica.
* Conteúdo de licença da DRM a entregar pelos serviços de entrega de licença: direitos, chave de conteúdo e as restrições.
* Tipo de **restrição de política de autorização da chave de conteúdo**: abrir, IP ou restrição de token.
* Se **token** tipo de **restrição de política de autorização da chave de conteúdo é utilizada**, a **restrição de política de autorização da chave de conteúdo** têm de ser cumpridos antes de uma licença é emitida.

### <a name="asset-delivery-policy"></a>Política de entrega de elemento

Através da configuração de uma política de entrega de elemento, pode controlar os seguintes atributos utilizados pelo packager dinâmico do AMS e a encriptação dinâmica de um ponto de final de transmissão em fluxo de AMS:

* Transmissão em fluxo de protocolo e a combinação de encriptação DRM, tais como DASH em CENC (PlayReady e Widevine), uniforme de transmissão em fluxo em PlayReady, HLS executado utilizando PlayReady ou Widevine.
* O predefinição/incorporado licenças URLs de entrega para cada um dos DRMs envolvidos.
* Se a licença aquisição URLs (LA_URLs) DASH MPD ou HLS listas de reprodução contêm a cadeia de consulta da chave ID (KID) para Widevine e do FairPlay, respetivamente.

## <a name="scenarios-and-samples"></a>Cenários e amostras

Com base na explicações na secção anterior, os seguintes cenários cinco híbridos utilizam o respetivo **chave de conteúdo**-**política de entrega de elemento** combinações de configuração (exemplos mencionados na última coluna siga a tabela):

|**Alojamento de & origem de conteúdo**|**Encriptação de DRM**|**Entrega de licença DRM**|**Configurar a chave de conteúdo**|**Configurar a política de entrega de elemento**|**Exemplo**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Sim|Sim|Exemplo 1|
|AMS|AMS|Terceiros|Sim|Sim|Exemplo 2|
|AMS|Terceiros|AMS|Sim|Não|Exemplo 3|
|AMS|Terceiros|Exterior|Não|Não|Exemplo 4|
|Terceiros|Terceiros|AMS|Sim|Não|    

Nos exemplos, proteção de PlayReady funciona para DASH e transmissão em fluxo uniforme. Os URLs de vídeos abaixo estão os URLs de transmissão em fluxo uniforme. Para obter os URLs correspondente do DASH, acrescente apenas "(formato = mpd-time-csf)". Pode utilizar o [Media Services do azure testar player](http://aka.ms/amtest) para testar num browser. Permite-lhe configurar o protocolo transmissão em fluxo a utilizar, em que técnico. IE11 e MS Edge no Windows 10 suportam PlayReady através de EME. Para obter mais informações, consulte [detalhes sobre a ferramenta de teste](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Exemplo 1

* URL (base) de origem: https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH & uniforme): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* LA_URL do FairPlay (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Exemplo 2

* URL (base) de origem: http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & uniforme): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Exemplo 3

* URL de origem: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & uniforme): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Exemplo 4

* URL de origem: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & uniforme): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Resumo

Em resumo, componentes DRM de serviços de suporte de dados do Azure são flexíveis, pode utilizá-los num cenário híbrido ao configurar corretamente a chave de conteúdo e a política de entrega de elemento, conforme descrito neste tópico.

## <a name="next-steps"></a>Passos seguintes
Vista dos Media Services percursos de aprendizagem.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

