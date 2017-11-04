---
title: "Analisar o suporte de dados através do portal do Azure | Microsoft Docs"
description: "Este tópico descreve como processar o seu suporte de dados com processadores de suporte de dados de análise de multimédia (MP) no portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 22032aef0cc8b7b015503043028873e70c21ee85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analise a sua multimédia com o portal do Azure
> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Descrição geral
Análise de serviços de multimédia do Azure é uma coleção de voz e visão componentes (na escala empresarial, a conformidade, a segurança e alcance global) que torna mais fácil das organizações e empresas conhecimentos acionáveis dos respetivos ficheiros de vídeo. Para obter mais descrição geral da análise de serviços de multimédia do Azure consulte [isto](media-services-analytics-overview.md) tópico. 

Este tópico descreve como processar o seu suporte de dados com processadores de suporte de dados de análise de multimédia (MP) no portal do Azure. Pacotes de gestão de análise de multimédia produzem ficheiros MP4 ou ficheiros JSON. Se um processador de multimédia produzir um ficheiro MP4, pode transferir progressivamente o ficheiro. Se um processador de multimédia produzir um ficheiro JSON, pode transferir o ficheiro a partir do Blob Storage do Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Escolha um recurso que pretende analisar
1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições**, selecione **Elementos**.  
   .
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selecione o elemento que gostaria de analisar e prima a **analisar** botão.
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. No **recurso de multimédia de processo com a análise de multimédia** janela, selecione o processador. 
   
    O resto do artigo explica porquê e como utilizar cada processador. 
5. Prima **criar** para iniciar uma tarefa.

## <a name="azure-media-indexer"></a>Azure Media Indexer
O **indexador de suporte de dados do Azure** processador de multimédia permite-lhe efetuar e o conteúdo de ficheiros de suporte de dados pesquisáveis, bem como gerar controla captioning fechada. Estas secções fornece alguns detalhes sobre as opções que pode especificar para este pacote de gestão.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Idioma
A linguagem natural para ser reconhecido no ficheiro multimedia. Por exemplo, inglês ou espanhol. 

### <a name="captions"></a>Legendas
Pode escolher um formato de legenda que será gerado a partir do seu conteúdo. Uma tarefa de indexação pode gerar ficheiros de legendas nos seguintes formatos:  

* **SAMI**
* **TTML**
* **WebVTT**

Fechado legenda (CC) estes formatos de ficheiros podem ser utilizados para tornar os ficheiros de áudio e vídeos acessível para pessoas com hearing disability.

### <a name="aib-file"></a>Ficheiro AIB
Selecione esta opção se pretender gerar o ficheiro de Blob de índice de áudio para utilização com a IFilter de servidor de SQL personalizados. Para obter mais informações, consulte [isto](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blogue.

### <a name="keywords"></a>Palavras-chave
Selecione esta opção se pretende gerar um ficheiro XML de palavras-chave. Este ficheiro contém palavras-chave extraídas do conteúdo reconhecimento de voz, com frequência e informações de deslocamento.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar a tarefa. [Isto](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que lhe permite identificar o conteúdo de saída. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Hyperlapse de multimédia do Azure é um pacote de gestão que cria uniforme vídeos passado o tempo do conteúdo primeira pessoa ou ação câmara.  Para obter mais informações, veja [este](media-services-hyperlapse-content.md) tópico. Estas secções fornece alguns detalhes sobre as opções que pode especificar para este pacote de gestão.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Velocidade
Especifique a velocidade a que acelerar o vídeo de entrada. O resultado é um rendition stabilized e passado o tempo do vídeo de entrada.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar a tarefa. [Isto](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que lhe permite identificar o conteúdo de saída. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
O **Azure suporte de dados enfrentam Detector** processador de multimédia (MP) permite-lhe contagem, controlar movimentos e mesmo meça a participação de público-alvo e reação através de expressões facial. Este serviço contém duas funcionalidades: 

* **Deteção de rostos em**
  
    Deteção de rostos em localiza e controla faces humanos dentro de um vídeo. Vários faces podem ser detetados e, subsequentemente, ser monitorizados como estes mover-se, com os metadados de hora e a localização devolvido num ficheiro JSON. Durante o registo, a tentativa de atribuir um ID de consistente para a mesma letra enquanto a pessoa que está a mover à volta no ecrã, mesmo que estes são obstructed ou deixam brevemente da moldura.
  
  > [!NOTE]
  > Este serviços não efetua o reconhecimento facial. Uma pessoa que mantém a frame ou fica obstructed para demasiado tempo terá um novo ID quando devolvem.
  > 
  > 
* **Deteção de emoções**
  
    Deteção de emoções é um componente opcional do processador do suporte de dados de deteção de rostos em que devolve o Analysis Services em vários atributos emotional de faces detetados, incluindo happiness, sadness, fear, anger e muito mais. 

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de deteção
Um dos seguintes modos de pode ser utilizado pelo processador:

* Deteção de rostos em
* por deteção de emoções de rostos em
* Deteção de emoções agregado

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar a tarefa. [Isto](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que lhe permite identificar o conteúdo de saída. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
O **Detector de movimento de suporte de dados do Azure** processador de multimédia (MP) permite-lhe identificar eficientemente secções de interesse num vídeo longo e uneventful caso contrário. Deteção de movimento pode ser utilizada no filmagens de câmaras estático para identificar secções do vídeo onde ocorre o movimento. Gera um ficheiro JSON que contém um metadados com carimbos região e o delimitador onde o evento ocorreu.

Esta tecnologia direcionado para os feeds de vídeo de segurança, é capaz de categorizar movimento eventos relevantes e falsos positivos, tais como sombras e alterações de lighting. Isto permite-lhe gerar alertas de segurança a partir de feeds da câmara sem ser spammed com eventos irrelevantes endless, ao conseguir extrair instantes de interesse de vídeos de vigilância extremamente longos.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Este processador pode ajudar a criar resumos dos vídeos longos selecionando automaticamente interessantes fragmentos do vídeo de origem. Isto é útil quando pretende fornecer uma rápida descrição geral que esperar um vídeo longo. Para obter informações detalhadas e exemplos, consulte [utilização do Azure Media as miniaturas de vídeo para criar um resumo de vídeo](media-services-video-summarization.md)

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar a tarefa. [Isto](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que lhe permite identificar o conteúdo de saída. 

## <a name="next-steps"></a>Passos seguintes
Vista dos Media Services percursos de aprendizagem.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

