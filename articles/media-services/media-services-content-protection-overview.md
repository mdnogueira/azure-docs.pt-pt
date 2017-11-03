---
title: "Proteger os seus conteúdos com Media Services do Azure | Microsoft Docs"
description: "Este artigo dar uma descrição geral da proteção de conteúdos com os Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 64be4ea104bd11b8e191e2c8d4170a2de88acb47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-content-overview"></a>Proteger conteúdo descrição geral
Os Serviços de Multimédia do Microsoft Azure permite-lhe proteger os seus elementos multimédia desde o momento em que saem do seu computador para armazenamento, processamento e entrega. Os Media Services permite-lhe distribuir os seus conteúdos em direto e a pedido dinamicamente encriptado com AES Advanced Encryption Standard () (utilizando as chaves de encriptação de 128 bits) ou qualquer uma dos DRMs principais: Microsoft PlayReady, Widevine da Google e do FairPlay da Apple. Os Media Services também fornecem um serviço para entrega de chaves AES e DRM licenças (PlayReady, Widevine e do FairPlay) para clientes autorizados. 

A imagem seguinte demonstra os fluxos de trabalho de proteção de conteúdos que suporte de AMS. 

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Este tópico explica [conceitos e terminologia](media-services-content-protection-overview.md) relevantes para compreender a proteção de conteúdos com AMS. O tópico também contém [ligações](media-services-content-protection-overview.md#common-scenarios) para tópicos que mostram como atingir as tarefas de proteção de conteúdos. 

## <a name="dynamic-encryption"></a>Encriptação dinâmica
Serviços de suporte de dados do Microsoft Azure permitem entregar o conteúdo encriptado dinamicamente, com o desmarcar chave AES ou encriptação DRM: Microsoft PlayReady, Widevine da Google e do FairPlay da Apple.

Atualmente, pode encriptar os seguintes formatos de transmissão em fluxo: HLS, MPEG DASH e transmissão em fluxo uniforme. Não é possível encriptar transferências de transferência progressiva.

Se pretender que para os serviços de suporte de dados encriptar um recurso, tem de associar o seu elemento uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) e também configurar políticas de autorização da chave.

Terá também de configurar a política de entrega de elementos. Se pretende transmitir um recurso encriptados de armazenamento, certifique-se de que especifica a forma como pretende entregá-lo através da política de entrega de elemento de configuração.

Quando um fluxo é solicitado por um leitor, os Media Services utiliza a chave especificada encriptar de forma dinâmica o conteúdo com o desmarcar chave AES ou encriptação DRM. Para desencriptar o fluxo, o leitor solicitará a chave do serviço de entrega de chave. Para decidir se pretende ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.


## <a name="storage-encryption"></a>Encriptação de armazenamento
Utilize a encriptação de armazenamento para encriptar o seu conteúdo transparente localmente utilizando AES 256 bits encriptação e, em seguida, carregue-o no Storage do Azure onde é armazenado encriptados em pausa. Os elementos protegidos com encriptação do storage são desencriptados automaticamente colocados no sistema de ficheiros encriptados antes da codificação e opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal motivo para a encriptação de armazenamento é quando pretender proteger os ficheiros de suporte de dados de entrada de alta qualidade com uma encriptação forte Inativos no disco.

Para fornecer um recurso encriptados de armazenamento, tem de configurar política de entrega de elementos para que os Media Services confie como pretende distribuir os seus conteúdos. Antes do elemento possa ser transmitido, o servidor de transmissão em fluxo remove a encriptação de armazenamento e fluxos de conteúdo através da política de entrega especificado (por exemplo, AES, encriptação comum ou sem encriptação).

## <a name="common-encryption-cenc"></a>Encriptação comum (CENC)
Encriptação comum é utilizada quando encriptar o conteúdo com PlayReady ou / e Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Utilizar a encriptação de cbcs-aapl
Cbcs-aapl é utilizado quando o conteúdo com o do FairPlay de encriptação.

## <a name="envelope-encryption"></a>Encriptação do envelope
Utilize esta opção se pretender proteger os seus conteúdos com AES-128 chave não encriptada. Se pretende uma opção mais segura, escolha uma dos DRMs listados neste tópico. 

## <a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves
Os Media Services fornecem um serviço para entrega de licenças DRM (PlayReady, Widevine, do FairPlay) e AES limpar as chaves para clientes autorizados. Pode utilizar [portal do Azure](media-services-portal-protect-content.md), REST API ou o SDK de Media Services para .NET para configurar políticas de autenticação e autorização para as suas licenças e chaves.

## <a name="token-restriction"></a>Restrição de token
A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização: aberto ou token restrito. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Os Media Services suportam tokens no formato simples Web Tokens (SWT) e formato JSON Web tokens (JWT). Serviços de suporte de dados não fornece serviços de Token seguro. Pode criar um STS personalizado ou tirar partido das ACS do Microsoft Azure para tokens de problema. O STS tem de ser configurado para criar o token assinado com especificado chave e emitir afirmações que especificou na configuração de restrição de token. O serviço de entrega de chave de Media Services irá devolver a chave de pedido (ou uma licença) para o cliente se o token é válido e as afirmações na correspondência de token que os que são configurados para a chave (ou uma licença).

Quando configurar o token restrito a política, tem de especificar a chave de verificação principal, o emissor e o parâmetros público-alvo. A chave de verificação principal contém a chave que o token foi assinado com, emissor é o serviço de token seguro que emite o token. O público-alvo (por vezes denominado âmbito) descreve a intenção do token ou o recurso autoriza o token de acesso a. O serviço de entrega de chave de Media Services valida que estes valores no token correspondem aos valores no modelo.

## <a name="streaming-urls"></a>URLs de transmissão em fluxo
Se o seu elemento foi encriptado com mais do que um DRM, deve utilizar uma etiqueta de encriptação no URL de transmissão em fluxo: (formato = 'm3u8-aapl' encriptação = 'xxx').

Aplicam as seguintes considerações:

* Apenas zero ou um tipo de encriptação pode ser especificado.
* Tipo de encriptação não tem de ser especificado no url, se apenas uma encriptação foi aplicada ao elemento.
* Tipo de encriptação é sensível a maiúsculas e minúsculas.
* Podem ser especificados os seguintes tipos de encriptação:  
  * **cenc**: encriptação comum (Playready ou Widevine)
  * **cbcs-aapl**: Fairplay
  * **CBC**: encriptação de envelope AES.

## <a name="common-scenarios"></a>Cenários comuns
Os tópicos seguintes demonstram como proteger o conteúdo no armazenamento, a entrega de multimédia de transmissão em fluxo encriptada de forma dinâmica, utilize o serviço de entrega de chave/licença do AMS

* [Proteger com AES](media-services-protect-with-aes128.md) 
* [Proteger com PlayReady e/ou Widevine](media-services-protect-with-drm.md)
* [Transmitir o seu protegidos de conteúdo HLS com do FairPlay da Apple e/ou PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Cenários adicionais
* [Como integrar o serviço de Azure PlayReady licença com o seu próprio servidor encriptador/de transmissão em fluxo](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
* [Utilizar castLabs para fornecer licenças DRM aos Media Services do Azure](media-services-castlabs-integration.md)

>[!NOTE]
>Um cenário em que utilizar um DRM server(technology) e o fluxo do AMS externo não é atualmente suportado.


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Anunciar PlayReady como um serviço e a encriptação dinâmica AES com Media Services do Azure](http://mingfeiy.com/playready)

[Preços de entrega de licença de PlayReady de serviços de suporte de dados do Azure explicado](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Como depurar para AES encriptados fluxo Media Services do Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT token authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar a aplicação do Azure Media Services OWIN MVC com base no Azure Active Directory e restringir a entrega de chave de conteúdo com base em afirmações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Utilizar o Azure ACS para tokens de problema](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
