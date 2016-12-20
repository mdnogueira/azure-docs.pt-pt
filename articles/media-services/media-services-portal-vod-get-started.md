---
title: " Introdução à entrega de conteúdos a pedido com o Portal do Azure | Microsoft Docs"
description: "Este tutorial explica os passos para implementar um serviço básico de entrega de conteúdos de Vídeo a Pedido (VoD) com a aplicação Azure Media Services (AMS) com o Portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 473ca94a4b081aee69fa06718790acd61a8b51b3


---
# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introdução à entrega de conteúdos a pedido com o Portal do Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Este tutorial explica os passos para implementar um serviço básico de entrega de conteúdos de Vídeo a Pedido (VoD) com a aplicação Azure Media Services (AMS) com o Portal do Azure.

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Este tutorial inclui as seguintes tarefas:

1. Criar uma conta dos Azure Media Services.
2. Configurar o ponto final de transmissão em fluxo.
3. Carregar um ficheiro de vídeo.
4. Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.
5. Publicar o elemento e obter os URLs de transferência progressiva e transmissão em fluxo.  
6. Reproduzir os conteúdos.

## <a name="create-an-azure-media-services-account"></a>Criar uma conta dos Azure Media Services
Os passos nesta secção explicam como criar uma conta dos AMS.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **+Novo** > **Web + Móvel** > **Serviços de Multimédia**.
   
    ![Criar Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. Em **CRIAR CONTA DE MEDIA SERVICES**, Introduza os valores necessários.
   
    ![Criar Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. Em **Nome da Conta**, introduza o nome da nova conta de AMS. Um nome de conta dos Media Services é composto por números ou letras minúsculas sem espaços, com 3 a 24 carateres de comprimento.
   2. Na subscrição, selecione entre as diferentes subscrições do Azure disponíveis para si.
   3. Em **Grupo de Recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é uma coleção de recursos que partilham o ciclo de vida, as permissões e as políticas. Sabia mais [aqui](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Em **Localização**, selecione a região geográfica que é utilizada para armazenar os registos de multimédia e metadados da conta dos Media Services. Esta região é utilizada para processar e transmitir em fluxo os seus conteúdos multimédia. Apenas as regiões dos Media Services disponíveis são apresentadas na caixa de lista pendente. 
   5. Em **Conta do Storage**, selecione uma conta do Storage para fornecer o Blob Storage do conteúdo de multimédia da conta de Media Services. Pode selecionar uma conta de armazenamento existente na mesma região geográfica da conta dos Serviços de Multimédia ou pode criar uma conta de armazenamento. É criada uma nova conta do Storage na mesma região. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.
      
       Saiba mais sobre armazenamento [aqui](../storage/storage-introduction.md).
   6. Selecione **Afixar no dashboard** para ver o progresso da implementação da conta.
4. Clique em **Criar** na parte inferior do formulário.
   
    Após criar a conta com êxito, o estado é alterado para **Em Execução**. 
   
    ![Definições dos Media Services](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Para gerir a sua conta de AMS (por exemplo, carregar vídeos, codificar elementos, monitorizar o progresso da tarefa) utilize a janela **Definições**.

## <a name="manage-keys"></a>Gerir Chaves
Necessita do nome da conta e das informações da chave primária para aceder programaticamente à conta de Media Services.

1. No Portal do Azure, selecione a sua conta. 
   
    A janela **Definições** aparece à direita. 
2. Na janela **Definições**, selecione **Chaves**. 
   
    A janela **Gerir chaves** mostra o nome da conta e as chaves primária e secundária são apresentadas. 
3. Prima o botão Copiar para copiar os valores.
   
    ![Chaves dos Media Services](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>Configurar os pontos finais de transmissão em fluxo
Ao trabalhar com os Azure Media Services, uma das situações mais comuns é a entrega de vídeo através de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Os Media Services suportam as seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), transmissão em Fluxo Uniforme, MPEG DASH e HDS (para detentores de licença do Adobe PrimeTime/acesso apenas).

Os Media Services fornecem um empacotamento dinâmico, permitindo a entrega dos seus conteúdos codificados em MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Media Services (MPEG DASH, HLS, Transmissão em Fluxo Uniforme, HDS) just-in-time, sem ter de voltar a armazenar versões pré-empacotadas de cada um destes formatos de transmissão em fluxo.

Para tirar partido do empacotamento dinâmico, tem de fazer o seguinte:

* Codificar o ficheiro (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável (os passos da codificação são demonstrados mais à frente neste tutorial).  
* Criar, pelo menos, uma unidade de transmissão em fluxo para o *ponto final de transmissão em fluxo* a partir do qual planeia entregar o conteúdo. Os passos abaixo mostram como alterar o número de unidades de transmissão em fluxo.

Com o empacotamento dinâmico, só tem de armazenar e pagar os ficheiros num único formato de armazenamento, e os Media Services compilam e disponibilizam a resposta adequada com base nos pedidos de um cliente.

Para criar e alterar o número de unidades reservadas para transmissão em fluxo, faça o seguinte:

1. Na janela **Definições**, clique em **Pontos finais de transmissão em fluxo**. 
2. Clique no ponto final de transmissão em fluxo predefinido. 
   
    A janela **DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO** é apresentada.
3. Para especificar o número de unidades de transmissão em fluxo, deslize o controlo de deslize **Unidades de transmissão em fluxo**.
   
    ![Unidades de transmissão em fluxo](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Clique no botão **Guardar** para guardar as alterações.
   
   > [!NOTE]
   > A alocação de quaisquer novas unidades pode demorar até 20 minutos a concluir.
   > 
   > 

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
Ao trabalhar com os Azure Media Services, uma das situações mais comuns é a distribuição de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Os Media Services suportam as seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), transmissão em Fluxo Uniforme, MPEG DASH e HDS (para detentores de licença do Adobe PrimeTime/acesso apenas). Para preparar os seus vídeos para transmissão em fluxo de velocidade de transmissão adaptável, terá de codificar o seu vídeo de origem para ficheiros de múltiplas velocidades de transmissão. Deve utilizar o **Codificador de Multimédia Standard** para codificar seus vídeos.  

Os Serviços de Multimédia também fornecem um empacotamento dinâmico, permitindo a entrega dos conteúdos codificados por Transmissão em Fluxo Uniforme ou MP4s de velocidade de transmissão adaptável nos formatos de transmissão em fluxo MPEG DASH, HLS, Smooth Streaming, ou HDS, sem ter de voltar a criar o pacote para estes formatos de transmissão em fluxo. Com o empacotamento dinâmico, só tem de armazenar e pagar os ficheiros num único formato de armazenamento, e os Media Services compilam e disponibilizam a resposta adequada com base nos pedidos de um cliente.

Para tirar partido do empacotamento dinâmico, tem de fazer o seguinte:

* Codificar o ficheiro de origem para um conjunto de ficheiros MP4 com velocidade de transmissão múltipla (os passos da codificação são demonstrados mais à frente neste tutorial).
* Obter, pelo menos, uma unidade de transmissão em fluxo para o ponto final de transmissão em fluxo a partir do qual planeia distribuir o conteúdo. Para obter mais informações, consulte [configurar pontos finais de transmissão em fluxo](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Para utilizar o portal para codificar
Esta secção descreve os passos que pode seguir para codificar o conteúdo com o Codificador de Multimédia Standard.

1. Na janela **Definições**, selecione **Elementos**.  
2. Na janela **Elementos**, selecione o elemento que gostaria de codificar.
3. Prima o botão **Codificar**.
4. Na janela **Codificar um elemento**, selecione o processador "Codificador de Multimédia Standard" e uma predefinição. Por exemplo, se sabe que o seu vídeo de entrada tem uma resolução de 1920 x 1080 pixels, então, pode utilizar a predefinição "H264 de Múltipla Velocidade de Transmissão a 1080p". Para obter mais informações sobre predefinições, consulte [este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artigo – é importante selecionar a predefinição mais adequada para o seu vídeo de entrada. Se tiver um vídeo de baixa resolução (640 x 360), então, não deve estar a utilizar a predefinição "H264 de Múltipla Velocidade de Transmissão a 1080p".
   
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

Para atualizar uma data de expiração num localizador, utilize as APIs [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Ao atualizar a data de expiração de um localizador SAS, o URL é alterado.

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

* Certifique-se de que o vídeo foi publicado.
* Este **Media Player** reproduz a partir do ponto final de transmissão em fluxo predefinido. Se pretender reproduzir a partir de um ponto final de transmissão em fluxo não predefinido, clique para copiar o URL e utilize outro leitor. Por exemplo, [Leitor dos Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Dec16_HO1-->


