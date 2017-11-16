---
title: Saiba mais sobre codificadores recomendadas pelo Media Services do Azure | Microsoft Docs
description: Saiba mais sobre codificadores recomendados pelos media services
services: media-services
keywords: "codificação codificadores; suporte de dados"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d0c5536d2339470eac058250cc14e1f250b86d90
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="recommended-on-premises-encoders"></a>Recomendada codificadores no local
Quando em direto de transmissão em fluxo com Media Services do Azure, pode especificar como pretende que o canal para receber o fluxo de entrada. Se optar por utilizar um codificador no local com um canal de codificação em direto, o codificador deve efetuar push de um fluxo de alta qualidade velocidade de transmissão única como saída. Se optar por utilizar um codificador no local com um pass-through canal, o codificador deve efetuar push de um fluxo de transmissão múltipla como saída com todos os qualities de saída pretendidas. Para obter mais informações, consulte [em direto com codificadores no local transmissão em fluxo](media-services-live-streaming-with-onprem-encoders.md).

Media Services do Azure recomenda utilizando um dos seguintes codificadores em direto que tenham RTMP como saída:
- Suporte de dados de Adobe Flash Live codificador 3.2
- Haivision Makito X HEVC
- Telestream Wirecast 8.1
- Setor de Teradek 756
- 8000 Transcodificadores
- Mini Transcodificadores HD-4

Media Services do Azure recomenda utilizando um dos seguintes codificadores em direto com velocidade de transmissão múltipla transmissão em fluxo uniforme como saída:
- Ateme TITAN em direto
- Codificador de multimédia digitais de Cisco 2200
- Elementar em direto
- Envivio 4Caster C4 Gen III
- Imagine comunicações Selenio MCP3
- Suporte de dados do Excel heroína em direto

> [!NOTE]
> Um codificador em direto pode enviar um fluxo de velocidade de transmissão única para um pass-through canal, mas esta configuração não é recomendada porque esta não permite a velocidade de transmissão adaptável de transmissão em fluxo para o cliente.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Como tornar-se de um parceiro de codificador no local
Como um parceiro de codificador de local de Media Services do Azure, os Media Services promove o produto, recomendamos o codificador para os clientes empresariais. Para se tornar um parceiro de codificador no local, tem de verificar a compatibilidade das suas codificador no local com os Media Services. Para tal, conclua as seguintes verificações:

Passar a verificação de canal
1. Criar ou visite a sua conta de Media Services do Azure
2. Crie e inicie um **pass-through** canal
3. Configure o codificador para enviar um transmissão múltipla em fluxo em direto.
4. Criar um evento em direto publicado
5. Executar o codificador em direto de, aproximadamente, 10 minutos
6. Pare o evento em direto
7. Registar o ID de recurso publicado URL de transmissão em fluxo para o arquivo em direto e as definições e a versão utilizada a partir do seu codificador em direto
8. Repor o estado de canal depois de criar cada amostra
9. Repita os passos 3 a 8 para todas as configurações suportados pelo seu codificador (com e sem ad sinalização/legendas/diferentes velocidades de codificação)

Verificação de canal de codificação em direto
1. Criar ou visite a sua conta de Media Services do Azure
2. Crie e inicie um **live encoding** canal
3. Configure o codificador para emitir um velocidade de transmissão única em fluxo em direto.
4. Criar um evento em direto publicado
5. Executar o codificador em direto de, aproximadamente, 10 minutos
6. Pare o evento em direto
7. Registar o ID de recurso publicado URL de transmissão em fluxo para o arquivo em direto e as definições e a versão utilizada a partir do seu codificador em direto
8. Repor o estado de canal depois de criar cada amostra
9. Repita os passos 3 a 8 para todas as configurações suportados pelo seu codificador (com e sem ad sinalização/legendas/várias velocidades de codificação)

Verificação de longevity
1. Criar ou visite a sua conta de Media Services do Azure
2. Crie e inicie um **pass-through** canal
3. Configure o codificador para enviar um transmissão múltipla em fluxo em direto.
4. Criar um evento em direto publicado
5. Execute o codificador em direto para uma semana ou superior
6. Pare o evento em direto
7. Registar o ID de recurso publicado URL de transmissão em fluxo para o arquivo em direto e as definições e a versão utilizada a partir do seu codificador em direto

Por último, enviar as definições de registado e em direto parâmetros de arquivo aos Media Services por relacionada amsstreaming@microsoft.com. Após a receção, os Media Services executa testes de verificação amostras da sua codificador em direto. Pode contactar os serviços de suporte de dados com quaisquer perguntas sobre este processo.