---
title: "Com parceiros para fornecer licenças Widevine aos Media Services do Azure | Microsoft Docs"
description: "Este artigo descreve como pode utilizar os serviços de suporte de dados do Azure (AMS) para fornecer um fluxo que é encriptado dinamicamente pelo AMS com PlayReady e Widevine DRMs. A licença PlayReady provém do servidor de licenças PlayReady de serviços de suporte de dados e a licença Widevine é enviada pelo servidor de licenças castLabs."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 6867e4f910970121df3858516c6bab3114c3c6f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização de parceiros para entregar licenças de Widevine para Serviços de Multimédia do Azure
## <a name="overview"></a>Descrição geral
Serviços de suporte de dados do Microsoft Azure permitem entregar MPEG-DASH protegido com Widevine DRM, que é encriptados de acordo com a especificação de encriptação comum (CENC).

Começando com o SDK de .NET de Media Services versão 3.5.2, os Media Services permite-lhe configurar o modelo de licença Widevine e obtenha licenças Widevine. Também pode utilizar os seguintes parceiros de AMS para o ajudar a fornecer licenças Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/).

## <a name="castlabs"></a>castLabs
Pode utilizar [castLabs](http://castlabs.com/company/partners/azure/) para fornecer licenças Widevine. Para obter mais informações, consulte [utilizar castLabs para fornecer DRM licenças para Media Services do Azure](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
Pode utilizar [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) para fornecer licenças Widevine. Para obter mais informações, consulte [utilizando Axinom para fornecer DRM licenças para Media Services do Azure](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar também
[Utilizar a encriptação comum dinâmica com o PlayReady e/ou o Widevine](media-services-protect-with-drm.md)

[Blogue do Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

