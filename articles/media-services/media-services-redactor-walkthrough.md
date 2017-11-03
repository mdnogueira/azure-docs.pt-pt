---
title: "Redact faces com instruções de análise de multimédia do Azure | Microsoft Docs"
description: "Este tópico mostra instruções passo a passo sobre como executar um fluxo de trabalho completo redaction utilizando o Explorador de serviços de suporte de dados do Azure (AMSE) e o Visualizador de Redactor de suporte de dados do Azure (ferramenta de código aberto)."
services: media-services
documentationcenter: 
author: Lichard
manager: cfowler
editor: 
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/03/2017
ms.author: rli; juliako;
ms.openlocfilehash: 0bd385ba78028a722c52cdf1508f3348ff90f05f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redact faces com instruções de análise de multimédia do Azure

## <a name="overview"></a>Descrição geral

**Azure Media Redactor** é um [análise de multimédia do Azure](media-services-analytics-overview.md) processador de multimédia (MP) que oferece redaction enfrentam dimensionável na nuvem. Enfrentam redaction permite-lhe modificar o vídeo para blur faces de indivíduos selecionados. Poderá pretender utilizar o serviço de redaction de rostos em cenários de segurança e de suporte de dados de notícias públicos. Alguns minutos de imagens que contém vários faces podem demorar horas a redact manualmente, mas com este serviço, o processo de redaction enfrentam necessitarão de apenas alguns passos simples. Para obter mais informações, consulte [isto](https://azure.microsoft.com/blog/azure-media-redactor/) blogue.

Para obter detalhes sobre **Redactor de suporte de dados do Azure**, consulte o [descrição geral de redaction enfrentam](media-services-face-redaction.md) tópico.

Este tópico mostra instruções passo a passo sobre como executar um fluxo de trabalho completo redaction utilizando o Explorador de serviços de suporte de dados do Azure (AMSE) e o Visualizador de Redactor de suporte de dados do Azure (ferramenta de código aberto).

Para obter mais informações, consulte [isto](https://azure.microsoft.com/en-us/blog/redaction-preview-available-globally) blogue.

## <a name="azure-media-services-explorer-workflow"></a>Fluxo de trabalho de Explorador de serviços de suporte de dados do Azure

A forma mais fácil para começar com Redactor consiste em utilizar a ferramenta AMSE de código aberto no github. Pode executar um fluxo de trabalho simplificado através de **combinado** modo se não precisa de acesso para o json de anotação ou as imagens de jpg enfrentam reside.

### <a name="download-and-setup"></a>Transferir e a configuração

1. Transferir a ferramenta AMSE do [aqui](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Inicie sessão sua conta de Media Services utilizando a chave do serviço.

    Para obter o nome de conta e as informações da chave, aceda ao [portal do Azure](https://portal.azure.com/) e selecione a sua conta AMS. Em seguida, selecione definições > chaves. A janela Gerir chaves mostra o nome da conta e as chaves primária e secundária são apresentadas. Copie os valores do nome da conta e a chave primária.

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Primeiro passar – analisar modo

1. Carregar o suporte de dados de ficheiros através do recurso –> carregamento ou através de arrastar e largar. 
1. Clique com o botão direito e processar o ficheiro de suporte de dados através da análise de multimédia –> Redactor de suporte de dados do Azure –> analisar modo. 


![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

O resultado incluirá um ficheiro de json anotações com dados de localização de letra, bem como um jpg de cada rosto detetado. 

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

###<a name="second-pass--redact-mode"></a>Segundo passar – redact modo

1. Carregar o elemento de vídeo original para a saída da primeira passagem e definido como um elemento principal. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Opcional) Carregar um ficheiro de 'Dance_idlist.txt', que inclui uma lista de tabulação delimitada de IDs de que pretende redact. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Opcional) Efetue as edições ao ficheiro annotations.json como aumentar os limites da caixa delimitador. 
4. Clique com o botão direito do rato em elemento de saída a partir da primeira passagem, selecione o Redactor e execute com a **Redact** modo. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Transferir ou partilha de elemento de saída redacted final. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

##<a name="azure-media-redactor-visualizer-open-source-tool"></a>Ferramenta de open source para suporte de dados Redactor Visualizador do Azure

Um código aberto [ferramenta Visualizador](https://github.com/Microsoft/azure-media-redactor-visualizer) foi concebido para ajudar os programadores a iniciar com o formato de anotações com análise e utilizando a saída.

Depois de clonar o repositório para executar o projeto, terá de transferir FFMPEG do respetivo [site oficial](https://ffmpeg.org/download.html).

Se for um programador tentar analisar os dados de anotação de JSON, procure no interior Models.MetaData exemplos de código de exemplo.

### <a name="set-up-the-tool"></a>Configurar a ferramenta

1.  Transfira e compilar a solução completa. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Transferir FFMPEG de [aqui](https://ffmpeg.org/download.html). Este projeto originalmente foi desenvolvido com versão be1d324 (2016-10-04) com ligação estático. 
3.  Copie ffmpeg.exe e ffprobe.exe na mesma pasta de saída como AzureMediaRedactor.exe. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Execute AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Utilize a ferramenta

1. Processar o vídeo na sua conta de Media Services do Azure com o pacote de gestão Redactor no modo de analisar. 
2. Transferir o ficheiro de vídeo original e o resultado a Redaction - analisar a tarefa. 
3. Execute a aplicação de Visualizador e escolha os ficheiros acima. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Pré-visualize o ficheiro. Selecione virado de frente gostaria de blur através de barra lateral no lado direito. 
    
    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  O campo de texto na parte inferior será atualizado com a IDs de letra. Crie um ficheiro chamado "idlist.txt" com estes IDs de como uma lista delimitada por avanço de página. 

    >[!NOTE]
    > O idlist.txt deve ser guardado num ANSI. Pode utilizar o bloco de notas para guardar no ANSI.
    
6.  Carregar este ficheiro para o elemento de saída do passo 1. Carregar o vídeo original para este recurso bem e definido como elemento principal. 
7.  Executar tarefa de Redaction neste recurso com o modo de "Redact" obter o final redacted as vídeo. 

## <a name="next-steps"></a>Passos seguintes 

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Anunciar enfrentam Redaction para análise de multimédia do Azure](https://azure.microsoft.com/blog/azure-media-redactor/)
