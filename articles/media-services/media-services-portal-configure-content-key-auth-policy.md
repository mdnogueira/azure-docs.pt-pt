---
title: "Configurar a política de autorização de chave de conteúdo no portal do Azure | Microsoft Docs"
description: "Saiba como configurar uma política de autorização para uma chave de conteúdo."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 5a35c7255a1c30a693862589c14f6a22a1900790
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-content-key-authorization-policy"></a>Configurar a política de autorização da chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Descrição geral
Serviços de suporte de dados do Microsoft Azure permite-lhe entregar MPEG-DASH, transmissão em fluxo uniforme e HTTP-Live-Streaming (HLS) protegidas com o padrão AES (Advanced Encryption) (utilizando as chaves de encriptação de 128 bits) ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS também permitem entregar transmissões em fluxo DASH encriptados com Widevine DRM. Tanto PlayReady como Widevine são encriptados de acordo com a especificação de Encriptação Comum (ISO/IEC 23001 7 CENC).

Os Media Services também fornecem uma **serviço de entrega de licença/chave** do que os clientes podem obter chaves AES ou licenças PlayReady/Widevine para reproduzir o conteúdo encriptado.

Este tópico mostra como utilizar o portal do Azure para configurar a política de autorização da chave de conteúdo. A chave mais tarde pode ser utilizada para encriptar o conteúdo de forma dinâmica. Tenha em atenção que atualmente pode encriptar transmissão seguintes formatos: HLS, MPEG DASH e transmissão em fluxo uniforme. Não é possível encriptar transferências de transferência progressiva.

Quando um leitor de solicita um fluxo que está definido como seja encriptado dinamicamente, o Media Services utiliza a chave configurada para encriptar o conteúdo através da encriptação AES ou DRM de forma dinâmica. Para desencriptar o fluxo, o leitor solicitará a chave do serviço de entrega de chave. Para decidir se pretende ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Se planear ter várias chaves de conteúdo ou pretender especificar um **serviço de entrega de licença/chave** URL que não seja o serviço de entrega de chave de Media Services, utilize o SDK .NET dos Media Services ou REST APIs.

[Configurar a política de autorização de chave de conteúdo utilizando o SDK .NET dos Media Services](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurar política de autorização de chave de conteúdo utilizando a API de REST dos serviços de suporte de dados](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>São aplicáveis algumas considerações:
* Quando a sua conta de AMS é criada um **predefinido** ponto final de transmissão em fluxo é adicionado à sua conta na **parado** estado. Para iniciar o conteúdo de transmissão em fluxo e tirar partido do empacotamento dinâmico e a encriptação dinâmica, o ponto final de transmissão em fluxo tem de ser o **executar** estado. 
* O elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável. Para obter mais informações, consulte [codificar um elemento](media-services-encode-asset.md).
* O serviço de entrega de chave coloca em cache ContentKeyAuthorizationPolicy e os respetivos objetos relacionados (opções de política e restrições) para 15 minutos.  Se criar um ContentKeyAuthorizationPolicy e especifique a utilização de uma restrição "Token", em seguida, testá-lo e, em seguida, atualizar a política a restrição "Abrir", irá demorar cerca de 15 minutos antes da política muda para a versão "Aberta" da política.

## <a name="how-to-configure-the-key-authorization-policy"></a>Como: configurar a política de autorização da chave
Para configurar a política de autorização da chave, selecione o **PROTEÇÃO de conteúdos** página.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter **abrir**, **token**, ou **IP** restrições de autorização (**IP** pode ser configurado com REST ou o .NET SDK).

### <a name="open-restriction"></a>Restrição aberta
O **abrir** restrição significa que o sistema irá fornecer a chave para qualquer pessoa que faz um pedido de chave. Esta restrição poderão ser úteis para fins de teste.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restrição de token
Para escolher a política de token restrito, prima a **TOKEN** botão.

O **token** política restrita tem de ser acompanhada de um token emitido por uma **serviço de Token seguro** (STS). Os Media Services suportam tokens no **Web Tokens simples** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) formato e **JSON Web Token** formato (JWT). Para informações, consulte [autenticação de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Serviços de suporte de dados não fornece **proteger serviços Token**. Pode criar um STS personalizado ou tirar partido das ACS do Microsoft Azure para tokens de problema. O STS tem de ser configurado para criar o token assinado com especificado chave e emitir afirmações que especificou na configuração de restrição de token. O serviço de entrega de chave de Media Services irá devolver a chave de encriptação para o cliente se o token é válido e as afirmações no token correspondem às configurado para a chave de conteúdo. Para obter mais informações, consulte [utilização do Azure ACS para tokens de problema](http://mingfeiy.com/acs-with-key-services).

Quando configurar o **TOKEN** política restrita, tem de definir os valores para **chave de verificação**, **emissor** e **público-alvo**. A chave de verificação principal contém a chave que o token foi assinado com, emissor é o serviço de token seguro que emite o token. O público-alvo (por vezes denominado âmbito) descreve a intenção do token ou o recurso autoriza o token de acesso a. O serviço de entrega de chave de Media Services valida que estes valores no token correspondem aos valores no modelo.

### <a name="playready"></a>PlayReady
Quando proteger o seu conteúdo com **PlayReady**, uma das ações tem de especificar na sua política de autorização é uma cadeia XML que define o modelo de licença PlayReady. Por predefinição, está definida a seguinte política:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"><LicenseTemplates> <PlayReadyLicenseTemplate> <AllowTestDevices>verdadeiro</AllowTestDevices> <ContentKey i:type="ContentEncryptionKeyFromHeader" /> <LicenseType>Nonpersistent</LicenseType> <PlayRight> <AllowPassingVideoContentToUnknownOutput>permitidos</AllowPassingVideoContentToUnknownOutput> </PlayRight> </PlayReadyLicenseTemplate> </LicenseTemplates></PlayReadyLicenseResponseTemplate>

Pode clicar no **importar o xml de política** botão e fornecer um XML diferente que está em conformidade com o esquema de XML definido [aqui](media-services-playready-license-template-overview.md).

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

