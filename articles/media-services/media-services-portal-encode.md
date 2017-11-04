---
title: "Codificar um elemento com o codificador de multimédia Standard no portal do Azure | Microsoft Docs"
description: "Este tutorial explica os passos da codificação de um recurso com o codificador de multimédia Standard no portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: ae5f4fd391cbf62b41d1a65f1d8107cefe3a5df3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Codificar um elemento com o codificador de multimédia Standard no portal do Azure

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Uma das situações mais comuns em trabalhar com Media Services do Azure é a distribuição de velocidade de transmissão adaptável de transmissão em fluxo para os seus clientes. Os Media Services suportam a velocidade de transmissão adaptável seguinte tecnologias de transmissão em fluxo: Apple HTTP Live Streaming (HLS), Microsoft transmissão em fluxo uniforme e dinâmico transmissão em fluxo adaptável através de HTTP (DASH, também denominado MPEG-DASH). Para preparar os seus vídeos para transmissão em fluxo de velocidade de transmissão adaptável, codificar o vídeo de origem como ficheiros de transmissão múltipla. Pode utilizar o Azure codificador de multimédia Standard para codificar seus vídeos.  

Os Serviços de Multimédia proporcionam-lhe empacotamento dinâmico. Com o empacotamento dinâmico, pode fornecer os seus MP4s de transmissão múltipla no HLS, transmissão em fluxo uniforme e MPEG-DASH, sem voltar a incluídas nestes formatos de transmissão em fluxo. Quando utiliza o empacotamento dinâmico, pode armazenar e pagar pelos ficheiros num formato de armazenamento única. Os serviços de suporte de dados baseia-se e serve a resposta adequada com base no pedido do cliente.

Para tirar partido do empacotamento dinâmico, tem de codificar o ficheiro de origem num conjunto de ficheiros MP4 de velocidade de transmissão múltipla. Os passos da codificação são demonstrados mais à frente neste artigo.

Para saber como dimensionar o processamento de suporte de dados, consulte [dimensionar o suporte de dados utilizando o portal do Azure de processamento](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Codificar no portal do Azure

Para codificar o conteúdo utilizando o codificador de multimédia Standard:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Selecione **Definições** > **Elementos**. Selecione o elemento que pretende codificar.
3. Selecione o botão **Codificar**.
4. No painel **Codificar um elemento**, selecione o processador **Media Encoder Standard** e uma predefinição. Para obter informações sobre as predefinições, veja [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Gerar automaticamente uma escala de velocidade de transmissão) e [Task presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Predefinições de tarefas para o Media Encoder Standard). É importante escolher a predefinição que funcionará melhor no seu vídeo de entrada. Por exemplo, se sabe que o seu vídeo de entrada tem uma resolução de 1920 &#215; 1080 pixels, pode escolher a predefinição **H264 Multiple Bitrate 1080p**. Se o vídeo for de baixa resolução (640 &#215; 360), não deve utilizar a predefinição **H264 Multiple Bitrate 1080p**.
   
   Para o ajudar a gerir os seus recursos, pode editar o nome do elemento de saída e o nome da tarefa.
   
   ![Codificar elementos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecione **Criar**.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar o progresso da tarefa codificação](media-services-portal-check-job-progress.md) no portal do Azure.  

