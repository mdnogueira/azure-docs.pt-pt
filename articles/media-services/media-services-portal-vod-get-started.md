---
title: "Introdução à entrega de VoD com o portal do Azure | Microsoft Docs"
description: "Este tutorial explica os passos para implementar um serviço básico de entrega de conteúdos de Vídeo a Pedido (VoD) com a aplicação Azure Media Services (AMS) com o Portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: cbb67ef92386a6288b3317bf77ebb67f15ce7fb2
ms.contentlocale: pt-pt
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introdução à entrega de conteúdos a pedido com o Portal do Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Este tutorial explica os passos para implementar um serviço básico de entrega de conteúdos de Vídeo a Pedido (VoD) com a aplicação Azure Media Services (AMS) com o Portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
O seguinte é necessário para concluir o tutorial:

* Uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Como Criar uma Conta de Media Services](media-services-portal-create-account.md).

Este tutorial inclui as seguintes tarefas:

1. Iniciar o ponto final de transmissão em fluxo.
2. Carregar um ficheiro de vídeo.
3. Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.
4. Publicar o elemento e obter os URLs de transferência progressiva e transmissão em fluxo.  
5. Reproduzir os conteúdos.

## <a name="start-streaming-endpoints"></a>Iniciar pontos finais de transmissão em fluxo 

Ao trabalhar com os Serviços de Multimédia do Azure, uma das situações mais comuns é a entrega de vídeo através de transmissão em fluxo de velocidade de transmissão adaptável. Os Serviços de Multimédia fornecem um empacotamento dinâmico, o que lhe permite entregar os seus conteúdos codificados em MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Serviços de Multimédia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter de armazenar versões pré-empacotadas de cada um destes formatos de transmissão em fluxo.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Para iniciar o ponto final de transmissão em fluxo, faça o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Na janela Definições, clique em Pontos finais de transmissão em fluxo. 
3. Clique no ponto final de transmissão em fluxo predefinido. 

    A janela DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO é apresentada.

4. Clique no ícone Início.
5. Clique no botão Guardar para guardar as alterações.

## <a name="upload-files"></a>Carregar ficheiros
Para transmitir vídeos em fluxo através dos Serviços de Multimédia do Azure, tem de carregar os vídeos de origem, codificá-los em múltiplas velocidades de transmissão e publicar o resultado. O primeiro passo é abrangido nesta secção. 

1. Na janela **Definição**, clique em **Elementos**.
   
    ![Carregar ficheiros](./media/media-services-portal-vod-get-started/media-services-upload.png)
2. Clique no botão **Carregar**.
   
    A janela **Carregar um elemento de vídeo** é apresentada.
   
   > [!NOTE]
   > Não existe qualquer limite de tamanho de ficheiro.
   > 
   > 
3. Navegue até ao vídeo pretendido no seu computador, selecione-o e clique em OK.  
   
    O carregamento inicia e pode ver o seu progresso sob o nome do ficheiro.  

Após a conclusão do carregamento, verá o novo elemento listado na janela **Elementos**. 

## <a name="encode-assets"></a>Codificar elementos

Ao trabalhar com os Azure Media Services, uma das situações mais comuns é a distribuição de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Os Serviços de Multimédia suportam as seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH. Para preparar os seus vídeos para transmissão em fluxo de velocidade de transmissão adaptável, terá de codificar o seu vídeo de origem para ficheiros de múltiplas velocidades de transmissão. Deve utilizar o **Codificador de Multimédia Standard** para codificar seus vídeos.  

Os Serviços de Multimédia também fornecem um empacotamento dinâmico, permitindo a entrega dos conteúdos codificados por Transmissão em Fluxo Uniforme ou MP4s de velocidade de transmissão múltipla nos formatos de transmissão em fluxo MPEG DASH, HLS, Smooth Streaming, sem ter de voltar a criar o pacote para estes formatos de transmissão em fluxo. Com o empacotamento dinâmico, só tem de armazenar e pagar os ficheiros num único formato de armazenamento, e os Media Services compilam e disponibilizam a resposta adequada com base nos pedidos de um cliente.

Para tirar partido do empacotamento dinâmico, tem de codificar o ficheiro de origem para um conjunto de ficheiros MP4 com velocidade de transmissão múltipla (os passos da codificação são demonstrados mais à frente nesta secção).

### <a name="to-use-the-portal-to-encode"></a>Para utilizar o portal para codificar
Esta secção descreve os passos que pode seguir para codificar o conteúdo com o Codificador de Multimédia Standard.

1. Na janela **Definições**, selecione **Elementos**.  
2. Na janela **Elementos**, selecione o elemento que gostaria de codificar.
3. Prima o botão **Codificar**.
4. Na janela **Codificar um elemento**, selecione o processador "Codificador de Multimédia Standard" e uma predefinição. Para obter informações sobre predefinições, veja [Gerar automaticamente uma escala de bits](media-services-autogen-bitrate-ladder-with-mes.md) e [Predefinições de Tarefas para MES](media-services-mes-presets-overview.md). Se planear controlar a predefinição de codificação a utilizar, não se esqueça: é importante selecionar a predefinição mais adequada ao vídeo de entrada. Por exemplo, se sabe que o seu vídeo de entrada tem uma resolução de 1920 x 1080 pixels, então, pode utilizar a predefinição "H264 de Múltipla Velocidade de Transmissão a 1080p". Se tiver um vídeo de baixa resolução (640 x 360), não deve estar a utilizar a predefinição "H264 Taxas de Bits Múltiplas 1080p".
   
   Para facilitar a gestão, tem uma opção de editar o nome do elemento de saída e o nome da tarefa.
   
   ![Codificar elementos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Prima **Criar**.

### <a name="monitor-encoding-job-progress"></a>Monitorizar o progresso da tarefa de codificação
Para monitorizar o progresso da tarefa de codificação, clique em **Definições** (na parte superior da página) e, em seguida, selecione **Tarefas**.

![Tarefas](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicar conteúdo
Para fornecer um URL ao utilizador que possa ser utilizado para transmitir ou transferir o conteúdo, primeiro precisa de "publicar" o elemento criando um localizador. Os localizadores fornecem acesso aos ficheiros contidos no elemento. Os Media Services suportam dois tipos de localizadores: 

* Os localizadores de transmissão em fluxo (OnDemandOrigin), utilizados para transmissão em fluxo adaptável (por exemplo, para transmissão em fluxo MPEG DASH, HLS ou Transmissão em Fluxo Uniforme). Para criar um localizador de transmissão, o seu elemento tem de conter um ficheiro .ism. 
* Os localizadores progressivos (SAS), utilizados para a entrega de vídeo através de transferência progressiva.

Um URL de transmissão em fluxo tem o seguinte formato e pode ser utilizado para reproduzir elementos de Transmissão em Fluxo Uniforme.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para compilar um URL de transmissão em fluxo HLS, acrescente (format=m3u8-aapl) ao URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para compilar um URL de transmissão em fluxo MPEG DASH, acrescente (format=mpd-time-csf) ao URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Um URL SAS tem o seguinte formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

> [!NOTE]
> Se utilizou o portal para criar localizadores antes de março de 2015, estes foram criados com uma data de expiração de dois anos.  
> 
> 

Para atualizar uma data de expiração num localizador, utilize as APIs [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Ao atualizar a data de expiração de um localizador SAS, o URL é alterado.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Para utilizar o portal para publicar um elemento
Para utilizar o portal para publicar um elemento, faça o seguinte:

1. Selecione **Definições** > **Elementos**.
2. Selecione o elemento que pretende publicar.
3. Clique no botão **Publicar**.
4. Selecione o tipo de localizador.
5. Prima **Adicionar**.
   
    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

O URL é adicionado à lista de **URLs Publicados**.

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdos a partir do portal
O Portal do Azure fornece um leitor de conteúdos que pode utilizar para testar o seu vídeo.

Clique no vídeo pretendido e, em seguida, clique no botão **Reproduzir**.

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

São aplicáveis algumas considerações:

* Para começar a transmissão em fluxo, inicie a execução do ponto final de transmissão em fluxo **predefinido**.
* Certifique-se de que o vídeo foi publicado.
* Este **Media Player** reproduz a partir do ponto final de transmissão em fluxo predefinido. Se pretender reproduzir a partir de um ponto final de transmissão em fluxo não predefinido, clique para copiar o URL e utilize outro leitor. Por exemplo, [Leitor dos Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


