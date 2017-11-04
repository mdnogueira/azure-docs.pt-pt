---
title: "Utilizar castLabs para fornecer licenças Widevine aos Media Services do Azure | Microsoft Docs"
description: "Este artigo descreve como pode utilizar os serviços de suporte de dados do Azure (AMS) para fornecer um fluxo que é encriptado dinamicamente pelo AMS com PlayReady e Widevine DRMs. A licença PlayReady provém do servidor de licenças PlayReady de serviços de suporte de dados e a licença Widevine é enviada pelo servidor de licenças castLabs."
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: 5b69e804809f834e81221fb2787a997a52dbe286
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do castLabs para entregar licenças de Widevine para Serviços de Multimédia do Azure
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Descrição geral
Este artigo descreve como pode utilizar os serviços de suporte de dados do Azure (AMS) para fornecer um fluxo que é encriptado dinamicamente pelo AMS com PlayReady e Widevine DRMs. A licença PlayReady provém do servidor de licenças PlayReady de serviços de suporte de dados e a licença Widevine é entregue por **castLabs** servidor de licenças.

A reprodução de transmissão em fluxo conteúdo protegido por CENC (PlayReady e/ou Widevine), pode utilizar [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Consulte [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) para obter mais detalhes.

O diagrama seguinte demonstra um alto nível Media Services do Azure e a arquitetura de integração castLabs.

![Integração](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Sistema típico de multimédia
* Conteúdo do suporte de dados é armazenado no AMS.
* Os IDs de chave das chaves de conteúdo são armazenados no castLabs e AMS.
* castLabs e AMS ter autenticação com token incorporada. As secções seguintes abordam tokens de autenticação. 
* Quando um cliente solicita para transmitir o vídeo, o conteúdo é encriptado dinamicamente com **encriptação comum** (CENC) e dinamicamente instaladores por AMS para transmissão em fluxo uniforme e travessão. Podemos também fornecer encriptação de fluxo de estatísticas básicas PlayReady M2TS para o protocolo de transmissão em fluxo HLS.
* Licenças PlayReady obtidas a partir do servidor de licenças do AMS e licença Widevine obtida a partir do servidor de licenças castLabs. 
* Media Player automaticamente decide que licença obter com base na capacidade da plataforma de cliente. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Geração de token de autenticação para obter uma licença
CastLabs e AMS suportam o formato de token JWT (JSON Web Token) utilizado para autorizar uma licença. 

### <a name="jwt-token-in-ams"></a>Token JWT no AMS
A tabela seguinte descreve o token JWT no AMS. 

| emissor | Cadeia de emissor a partir da escolhida Secure Token serviço (STS) |
| --- | --- |
| Audiência |Cadeia de audiência de STS utilizado |
| afirmações |Um conjunto de afirmações |
| NotBefore |Início de validade do token |
| Expirar |Fim de validade do token |
| SigningCredentials |A chave que é partilhada entre o servidor de licenças PlayReady, castLabs servidor de licenças e STS, poderia ser simétrico ou assimétrico chave. |

### <a name="jwt-token-in-castlabs"></a>Token JWT no castLabs
A tabela seguinte descreve o token JWT no castLabs. 

| Nome | Descrição |
| --- | --- |
| optData |Uma cadeia JSON que contém informações sobre si. |
| CRT |Uma cadeia JSON que contém informações sobre o elemento, os direitos de informações e reprodução de licença. |
| iat |A datetime atual no época. |
| jti |Um identificador exclusivo sobre este token (cada token só pode ser utilizado uma vez no sistema castLabs). |

## <a name="sample-solution-set-up"></a>Solução de exemplo de multimédia
O [solução de exemplo](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consiste em dois projetos:

* Uma aplicação de consola que pode ser utilizada para definir restrições DRM sobre um recurso já transmissões em, para PlayReady e Widevine.
* Uma aplicação Web que passa os tokens, pelo que podem ser vistos como uma versão muito simplificado de um STS.

Para utilizar a aplicação de consola:

1. Altere o App. config para configurar as credenciais do AMS, castLabs credenciais, configuração de STS e a chave partilhada.
2. Carregue um recurso para AMS.
3. Obter o UUID do elemento carregado e alterar 32 de linha no ficheiro Program.cs:
   
      var objIAsset = _context. Assets.Where (x = > x.Id = = "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Utilize um AssetId para atribuir o nome de recurso no sistema castLabs (44 linha no ficheiro Program.cs).
   
   Tem de definir AssetId para **castLabs**; tem de ser uma cadeia alfanumérica exclusiva.
5. Execute o programa.

Para utilizar a aplicação Web (STS):

1. Altere o Web. config para merchant do programa de configuração castlabs ID, a configuração de STS e a chave partilhada.
2. Implemente Web sites do Azure.
3. Navegue para o Web site.

## <a name="playing-back-a-video"></a>Reproduzir novamente um vídeo
A reprodução um vídeo encriptado com encriptação comum (PlayReady e/ou Widevine), pode utilizar o [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Quando executar a aplicação de consola, o ID de chave de conteúdo e o URL do manifesto são echoed.

1. Abra um novo separador e iniciar os STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Aceda a [Media Player do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Cole o URL de transmissão em fluxo.
4. Clique em de **opções avançadas** caixa de verificação.
5. No **proteção** lista pendente, selecione PlayReady e/ou Widevine.
6. Cole o token que recebeu do seu STS na caixa de texto de Token. 
   
   O servidor de licenças castLab não precisa de "portador =" prefixo à frente do token. Por isso, remova que antes de submeter o token.
7. O leitor de atualização.
8. Deve ser reprodução de vídeo.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

