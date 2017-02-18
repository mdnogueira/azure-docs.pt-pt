---
title: " Carregar ficheiros para uma conta dos Serviços de Multimédia com o portal do Azure | Microsoft Docs"
description: "Este tutorial orienta-o ao longo dos passos para carregar ficheiros para uma conta dos Serviços de Multimédia com o portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ed8ea30b87c8086d41cab879acce82062f08b31c
ms.openlocfilehash: f27ab42ab3c7c704804b9a5493c8b3acd954decb


---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Carregar ficheiros para uma conta dos Serviços de Multimédia com o portal do Azure
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 


Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. O Elemento pode conter ficheiros de vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e legendas (e os metadados relativos a esses ficheiros). Assim que os ficheiros são carregados, o seu conteúdo é armazenado em segurança na nuvem para processamento adicional e a transmissão em fluxo.


## <a name="upload-files"></a>Carregar ficheiros

>[!NOTE]
>Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. Veja [este](media-services-quotas-and-limitations.md) tópico para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.
>

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. No painel **Definições**, clique em **Recursos**.
   
    ![Carregar ficheiros](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. Clique no botão **Carregar**.
   
    A janela **Carregar um elemento de vídeo** é apresentada.
   
   > [!NOTE]
   > Não existe qualquer limite de tamanho de ficheiro.
   > 
   > 
4. Navegue até ao vídeo pretendido no seu computador, selecione-o e clique em OK.  
   
    O carregamento inicia e pode ver o seu progresso sob o nome do ficheiro.  

Após a conclusão do carregamento, verá o novo elemento listado na janela **Elementos**. 

## <a name="next-steps"></a>Passos seguintes
Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)

Também pode utilizar as Funções do Azure para acionar uma tarefa de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO2-->


