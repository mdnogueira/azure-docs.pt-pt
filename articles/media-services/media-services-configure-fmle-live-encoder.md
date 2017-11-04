---
title: "Configurar o codificador FMLE para enviar uma transmissão em fluxo em direto | Microsoft Docs"
description: "Este tópico mostra como configurar o codificador de Flash suporte de dados em direto codificador (FMLE) para enviar uma transmissão em fluxo para canais de AMS que estão ativados para live encoding."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: e831048f34ecf6e89595adc4bfd58b5977e04bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizar o codificador FMLE para enviar uma transmissão em fluxo em direto
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Elementar em direto](media-services-configure-elemental-live-encoder.md)
> * [Transcodificadores](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Este tópico mostra como configurar o [codificador em direto de Flash multimédia](http://www.adobe.com/products/flash-media-encoder.html) codificador (FMLE) para enviar uma transmissão em fluxo para AMS canais de consumo que está ativado para live encoding. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir os serviços de suporte de dados do Azure (AMS) com a ferramenta Explorador de serviços de suporte de dados do Azure (AMSE). Esta ferramenta é executada apenas em PCs Windows. Se estiver no Mac ou Linux, utilize o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

Tenha em atenção que este tutorial descreve AAC a utilizar. No entanto, FMLE não suporta AAC por predefinição. Terá de adquirir um plug-in para AAC codificação tais como MainConcept: [Plug-in do AAC](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>Pré-requisitos
* [Criar uma conta de Media Services do Azure](media-services-portal-create-account.md)
* Certifique-se existe um ponto de final de transmissão em fluxo em execução. Para obter mais informações, consulte [gerir transmissão em fluxo pontos finais numa conta de Media Services](media-services-portal-manage-streaming-endpoints.md)
* Instale a versão mais recente do [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) ferramenta.
* Inicie a ferramenta e ligue à sua conta de AMS.

## <a name="tips"></a>Sugestões
* Sempre que possível, utilize uma ligação de internet hardwired.
* É uma boa regra prática ao determinar os requisitos de largura de banda duplo a forma de transmissão em fluxo. Embora não seja um requisito obrigatório, irá ajudar a mitigar o impacto de congestionamento de rede.
* Quando utilizar o software com base no codificadores, feche enviados quaisquer programas desnecessários.

## <a name="create-a-channel"></a>Criar um canal
1. Na ferramenta AMSE, navegue para o **em direto** separador e o botão direito do rato clique na área de canal. Selecione **canal de criar...** no menu.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições de canal, selecione **padrão** para a opção Live Encoding, com o protocolo de entrada definido como **RTMP**. Pode deixar a todas as outras definições conforme está.

    Certifique-se de que o **iniciar agora o novo canal** está selecionada.

3. Clique em **criar canal**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos para iniciar.
>
>

Enquanto está a iniciar o canal pode [configurar codificador](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Tenha em atenção que a faturação é iniciada assim que o canal entra no estado pronto. Para obter mais informações, consulte [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Configurar o codificador FMLE
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

### <a name="configuration-steps"></a>Passos de configuração
1. Navegue para o Flash Live codificador de multimédia (FMLE) de interface no computador que está a ser utilizado.

    A interface é uma página principal de definições. Tome nota das seguintes definições para começar a trabalhar com transmissão em fluxo utilizando FMLE recomendadas.

   * Formato: Taxa de moldura 264: 30.00
   * Tamanho de entrada: 1280 x 720
   * Taxa de bits: Kbps de 5000 (pode ser ajustado com base no limitações de rede)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Quando utilizar entrelaçadas origens, volte a marca de verificação a opção "Deinterlace"
2. Selecione o ícone de chave inglesa junto ao formato, estas definições adicionais que devem ser:

   * Perfil: principal
   * Nível: 4.0
   * Frequência de Keyframe: segundos de 2

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Defina a seguinte definição de áudio importante:

   * Formato: AAC
   * Frequência de amostragem: Hz 44100
   * Velocidade de transmissão: Kbps 192

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Obter o canal de entrada URL para poder atribuí-la para o FMLE **RTMP Endpoint**.

    Navegue de volta para a ferramenta AMSE e verificar o estado de conclusão de canal. Depois do estado mudou de **inicial** para **executar**, pode obter o URL de entrada.

    Quando o canal está em execução, clique com o botão direito do rato no nome de canal, navegue para baixo até paire sobre **URL de entrada de copiar para área de transferência** e, em seguida, selecione **primário URL de entrada**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Colar estas informações no **FMS URL** campo da secção de saída e atribuir um nome de fluxo.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Para fins de redundância adicional, repita estes passos com o URL de entrada secundário.
6. Selecione **Ligar**.

> [!IMPORTANT]
> Antes de clicar em **Connect**, **tem** Certifique-se de que o canal está pronto.
> Além disso, certifique-se não manter o canal no estado pronto sem uma entrada contribuição feed durante um período superior > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e clique com o botão direito do rato no canal a ser testada. No menu, coloque o cursor sobre **reprodução a pré-visualização** e selecione **com o Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Se a sequência aparecer num leitor de, em seguida, o codificador foi configurado corretamente para ligar ao AMS.

Se não for recebido um erro, o canal, terá de ser reposto e definições de codificador ajustado. Consulte o [resolução de problemas](media-services-troubleshooting-live-streaming.md) tópico para obter orientações.  

## <a name="create-a-program"></a>Criar um programa
1. Depois de reprodução de canal é confirmada, crie um programa. Sob o **em direto** separador a ferramenta AMSE, com o botão direito clique na área de programa e selecione **criar um novo programa**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Nome do programa e, se necessário, ajuste o **duração da janela de arquivo** (que está predefinida para 4 horas). Também pode especificar uma localização de armazenamento ou deixe como predefinição.  
3. Verifique o **iniciar agora o programa** caixa.
4. Clique em **criar programa**.  

    >[!NOTE]
    >Criação de programa demora menos tempo que a criação do canal.
        
5. Assim que estiver a executar o programa, confirme reprodução, clicando com o botão direito do rato no programa e navegar até **reprodução de programas** e, em seguida, selecionar **com o Azure Media Player**.  
6. Depois de confirmar, com o botão direito clique no programa novamente e selecione **copie o URL de saída para a área de transferência** (ou obter estas informações do **definições e as informações do programa** opção no menu).

O fluxo está agora pronto para ser incorporado num leitor, distribuída ou para um público-alvo para uma visualização em direto.  

## <a name="troubleshooting"></a>Resolução de problemas
Consulte o [resolução de problemas](media-services-troubleshooting-live-streaming.md) tópico para obter orientações.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
