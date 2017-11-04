---
title: "Desenvolver aplicações de leitor de vídeo"
description: "O tópico fornece ligações para estruturas do leitor e plug-ins que pode utilizar para desenvolver as suas próprias aplicações de cliente que podem consumir a transmissão de multimédia de Media Services."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: 0e88baed8188890e80d4c2e7ee9d510fdabf6e43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-video-player-applications"></a>Desenvolver aplicações de leitor de vídeo
## <a name="overview"></a>Descrição geral
Os Media Services do Azure fornecem as ferramentas necessárias para criar aplicações avançadas e dinâmicas de leitor de cliente para a maioria das plataformas, incluindo: Dispositivos iOS dispositivos, Dispositivos Android, Windows, Windows Phone, Xbox e Caixas descodificadoras. Este tópico também fornece ligações para SDKs e estruturas do leitor que pode utilizar para desenvolver as suas próprias aplicações de cliente que podem consumir multimédia de transmissão em fluxo de Media Services do Azure.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
 
## <a name="azure-media-player"></a>Media Player do Azure
[Azure Media Player](http://aka.ms/ampinfo) é um leitor de vídeo da web incorporado reproduzir conteúdo de multimédia de Media Services do Microsoft Azure numa ampla variedade de dispositivos e browsers. Azure Media Player utiliza as normas do sector, como HTML5, extensões de origem de suporte de dados (MSE) e as extensões de suporte de dados encriptados (EME) para fornecer uma experiência de transmissão em fluxo adaptável avançada. Quando estes padrões não estão disponíveis num dispositivo ou num browser, o leitor de multimédia do Azure utiliza Flash e Silverlight como tecnologia de contingência. Independentemente da tecnologia de reprodução utilizada, os programadores terá uma interface unificada de JavaScript para acesso a APIs. Isto permite conteúdos servidos pelo Media Services do Azure para ser reproduzido através de um intervalo de nível de dispositivos e browsers sem qualquer esforço adicional.

Serviços de suporte de dados do Microsoft Azure permite conteúdo servido com DASH, transmissão em fluxo uniforme e formatos de transmissão em fluxo HLS para reproduzir o conteúdo novamente. Azure Media Player tem em consideração estes vários formatos e reproduz automaticamente a ligação melhor com base nas capacidades de plataforma/browser. Media Services do Microsoft Azure permite também a encriptação dinâmica de recursos com encriptação PlayReady ou AES-128 bits encriptação envelope. Azure Media Player permite a desencriptação de PlayReady e AES-128 bits conteúdo encriptado quando adequadamente configurado. 

Para mais informações:

* [Media Player do Azure](http://aka.ms/ampinfo)
* [Documentação do leitor de Media Services do Azure](http://aka.ms/ampdocs) 
* [Leitor de Media Services do Azure ao obter iniciado blogue](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Inscreva-se para se manter atualizado com a versão mais recente do Azure Media Player](http://aka.ms/ampsignup)
* [Adicionar novos pedidos de funcionalidades, ideias, comentários](http://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Outras ferramentas para criar aplicações do leitor
Também pode utilizar qualquer um dos SDKs seguintes:

* [Uniforme SDK de cliente de transmissão em fluxo](http://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Aplicação da loja Windows transmissão em fluxo uniforme](media-services-build-smooth-streaming-apps.md)
* [Plataforma de suporte de dados da Microsoft: Arquitetura de leitor](http://playerframework.codeplex.com/) 
* [HTML5 Documentação leitor do Framework](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Uniforme Microsoft Plug-in de transmissão em fluxo para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licenciamento Microsoft® uniforme transmissão em fluxo cliente transferências Kit](http://aka.ms/sspk) 
* [Desenvolvimento de aplicações de vídeo do XBOX](http://xbox.create.msdn.com/) 

## <a name="advertising"></a>Publicidade
Media Services do Azure fornece suporte para inserção de ad através da plataforma de suporte de dados do Windows: estruturas do leitor. Estruturas do leitor com suporte ad estão disponíveis para dispositivos Windows 8, o Silverlight, o Windows Phone 8 e iOS. Cada framework player contém o código de exemplo que mostra como implementar uma aplicação do leitor. Existem três tipos diferentes de anúncios que pode inserir o suporte de dados:

Linear – anúncios de moldura completo que colocar em pausa o vídeo principal

Nonlinear – anúncios de sobreposição que são apresentados como reprodução de vídeo principal, normalmente, um logótipo ou outra imagem estática colocado num leitor de

Complementar – anúncios que são apresentadas fora do leitor de

Anúncios podem ser colocados em qualquer momento na linha de tempo as vídeo principal. Tem dizer o leitor quando reproduzir o ad e os anúncios para reproduzir. Isto é feito utilizando um conjunto de ficheiros padrão baseado em XML: modelo de serviço de Ad de vídeo (VAST), Digital vídeo vários Ad listas de reprodução (VMAP), modelo de sequenciação Abstrato suporte de dados (MAST) e Digital vídeo Player Ad Interface definição (VPAID). Ficheiros VASTA especificar que anúncios para apresentar. Ficheiros VMAP Especifique quando reproduzir vários anúncios e conter XML grande. Os ficheiros MAST são outra forma de anúncios de sequência que também pode conter XML grande. Ficheiros VPAID definem uma interface entre o leitor de vídeo e o ad ou o servidor do ad. Para obter mais informações, consulte [inserir anúncios](https://msdn.microsoft.com/library/dn387398.aspx).

Para obter informações sobre captioning fechado e o suporte de anúncios na vídeos de transmissão em fluxo em direto, consulte [suportado fechado Captioning e normas de inserção de Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja Também
[Incorporar um Vídeo de Transmissão em fluxo Adaptivo MPEG-DASH numa Aplicação HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repositório do GitHub dash.js](https://github.com/Dash-Industry-Forum/dash.js)

