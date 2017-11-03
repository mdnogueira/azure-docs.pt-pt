---
title: "Configurar o codificador em direto Elemental para enviar uma transmissão em fluxo em direto | Microsoft Docs"
description: "Este tópico mostra como configurar o codificador em direto Elemental para enviar uma transmissão em fluxo para canais de AMS que estão ativados para live encoding."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.openlocfilehash: 668a3ab46a70c0ee25fa87031d27c0f4333ec89c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizar o codificador em direto Elemental para enviar uma transmissão em fluxo em direto
> [!div class="op_single_selector"]
> * [Elementar em direto](media-services-configure-elemental-live-encoder.md)
> * [Transcodificadores](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Este tópico mostra como configurar o [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) codificador para enviar uma transmissão em fluxo para AMS canais de consumo que está ativado para live encoding.  Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir os serviços de suporte de dados do Azure (AMS) com a ferramenta Explorador de serviços de suporte de dados do Azure (AMSE). Esta ferramenta é executada apenas em PCs Windows. Se estiver no Mac ou Linux, utilize o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Pré-requisitos
* Tem de ter um conhecimento prático de utilização em direto Elemental a interface web para criar eventos em direto.
* [Criar uma conta de Media Services do Azure](media-services-portal-create-account.md)
* Certifique-se existe um ponto de final de transmissão em fluxo em execução. Para obter mais informações, consulte [gerir transmissão em fluxo pontos finais numa conta de Media Services](media-services-portal-manage-streaming-endpoints.md).
* Instale a versão mais recente do [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) ferramenta.
* Inicie a ferramenta e ligue à sua conta de AMS.

## <a name="tips"></a>Sugestões
* Sempre que possível, utilize uma ligação de internet hardwired.
* É uma boa regra prática ao determinar os requisitos de largura de banda duplo a forma de transmissão em fluxo. Embora não seja um requisito obrigatório, irá ajudar a mitigar o impacto de congestionamento de rede.
* Quando utilizar o software com base no codificadores, feche enviados quaisquer programas desnecessários.

## <a name="elemental-live-with-rtp-ingest"></a>Elemental em direto com RTP de inserção
Esta secção mostra como configurar o codificador em direto Elemental que envia uma transmissão em fluxo em direto sobre RTP.  Para obter mais informações, consulte [fluxo MPEG-TS sobre RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Criar um canal

1. Na ferramenta AMSE, navegue para o **em direto** separador e o botão direito do rato clique na área de canal. Selecione **canal de criar...** no menu.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições de canal, selecione **padrão** para a opção Live Encoding, com o protocolo de entrada definido como **RTP (MPEG-TS)**. Pode deixar a todas as outras definições conforme está.

    Certifique-se de que o **iniciar agora o novo canal** está selecionada.

3. Clique em **criar canal**.

   ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> O canal pode demorar até 20 minutos para iniciar.
>
>

Enquanto está a iniciar o canal pode [configurar codificador](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Tenha em atenção que a faturação é iniciada assim que o canal entra no estado pronto. Para obter mais informações, consulte [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>Configurar o codificador em direto Elemental
Neste tutorial, são utilizadas as seguintes definições de saída. As restantes desta secção descreve os passos de configuração mais detalhadamente.

**Vídeo**:

* Codec: 264
* Perfil: Alta (nível 4.0)
* Velocidade de transmissão: kbps 5000
* Keyframe: 2 segundos (60 segundos)
* Taxa de moldura: 30

**Áudio**:

* O codec: AAC (LC)
* Velocidade de transmissão: kbps 192
* Frequência de amostragem: kHz 44.1

#### <a name="configuration-steps"></a>Passos de configuração
1. Navegue para o **Elemental Live** web interface e configurar o codificador para **UDP/TS** de transmissão em fluxo.
2. Assim que for criado um novo evento, desloque para baixo para os grupos de saída e adicione o **UDP/TS** grupo de saída.
3. Criar uma nova saída selecionando **novo fluxo** e, em seguida, clicando em **adicionar saída**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > Recomenda-se que o evento Elemental tem timecode definido como "Relógio do sistema" para ajudar o codificador de restabelecer ligação no caso de uma falha de fluxo.
   >
   >
4. Agora que o resultado foi criado, clique em **adicionar fluxo**. Agora podem ser configuradas as definições de saída.
5. Desloque para baixo até o "fluxo 1" que acabou de criar, clique em de **vídeo** separador à esquerda e expanda o **avançadas** secção de definições.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Enquanto Elemental Live tem uma vasta gama de personalização disponíveis, são recomendadas as seguintes definições para começar a trabalhar com transmissão em fluxo para AMS.

   * Resolução: 1280 x 720
   * Framerate: 30
   * GOP tamanho: 60 frames Jumbo
   * Modo de interlace: transferência progressiva
   * Velocidade de transmissão: 5000000 bits/s (Isto pode ser ajustado com base no limitações de rede)

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. Obter o URL do canal de entrada.

    Navegue de volta para a ferramenta AMSE e verificar o estado de conclusão de canal. Depois do estado mudou de **inicial** para **executar**, pode obter o URL de entrada.

    Quando o canal está em execução, clique com o botão direito do rato no nome de canal, navegue para baixo até paire sobre **URL de entrada de copiar para área de transferência** e, em seguida, selecione **primário URL de entrada**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Colar estas informações no **primário de destino** campo a elementar. Todas as outras definições podem permanecer a predefinição.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Para fins de redundância adicional, repita estes passos com o URL de entrada secundário através da criação de um separador de "Saída" separado para UDP/TS de transmissão em fluxo.
3. Clique em **criar** (se foi criado um novo evento) ou **atualização** (se a edição de um evento previamente existente) e, em seguida, avance para iniciar o codificador.

> [!IMPORTANT]
> Antes de clicar em **iniciar** na interface de web em direto Elemental, **tem** Certifique-se de que o canal está pronto.
> Além disso, certifique-se não manter o canal no estado pronto sem um evento durante um período superior > 15 minutos.
>
>

Depois do fluxo tem estado em execução durante 30 segundos, navegue para a ferramenta e teste de reprodução AMSE.  

### <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e clique com o botão direito do rato no canal a ser testada. No menu, coloque o cursor sobre **reprodução a pré-visualização** e selecione **com o Azure Media Player**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Se a sequência aparecer num leitor de, em seguida, o codificador foi configurado corretamente para ligar ao AMS.

Se não for recebido um erro, o canal, terá de ser reposto e definições de codificador ajustado. Consulte o [resolução de problemas](media-services-troubleshooting-live-streaming.md) tópico para obter orientações.   

### <a name="create-a-program"></a>Criar um programa
1. Depois de reprodução de canal é confirmada, crie um programa. Sob o **em direto** separador a ferramenta AMSE, com o botão direito clique na área de programa e selecione **criar um novo programa**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. Nome do programa e, se necessário, ajuste o **duração da janela de arquivo** (que está predefinida para 4 horas). Também pode especificar uma localização de armazenamento ou deixe como predefinição.  
3. Verifique o **iniciar agora o programa** caixa.
4. Clique em **criar programa**.  

    >[!NOTE]
    > Criação de programa demora menos tempo que a criação do canal.   
      
5. Assim que estiver a executar o programa, confirme reprodução, clicando com o botão direito do rato no programa e navegar até **reprodução de programas** e, em seguida, selecionar **com o Azure Media Player**.  
6. Depois de confirmar, com o botão direito clique no programa novamente e selecione **copie o URL de saída para a área de transferência** (ou obter estas informações do **definições e as informações do programa** opção no menu).

O fluxo está agora pronto para ser incorporado num leitor, distribuída ou para um público-alvo para uma visualização em direto.  

## <a name="troubleshooting"></a>Resolução de problemas
Consulte o [resolução de problemas](media-services-troubleshooting-live-streaming.md) tópico para obter orientações.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
