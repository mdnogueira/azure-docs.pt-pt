<properties
    pageTitle=" Introdução à distribuição de conteúdos a pedido utilizando o Portal Clássico do Azure | Microsoft Azure"
    description="Este tutorial vai ajudá-lo a implementar uma aplicação de distribuição de conteúdos de Vídeo a Pedido (VoD) com os Media Services do Azure utilizando o Portal Clássico do Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/22/2016"
    ms.author="juliako"/>


# Introdução à distribuição de conteúdos a pedido utilizando o Portal Clássico do Azure


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


Este tutorial vai ajudá-lo a implementar uma aplicação básica de distribuição de conteúdos de Vídeo a Pedido (VoD) utilizando o Portal Clássico do Azure.

> [AZURE.NOTE] Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 


Este tutorial inclui as seguintes tarefas:

1.  Criar uma conta dos Media Services do Azure.
2.  Configurar o ponto final de transmissão em fluxo.
1.  Carregar um ficheiro de vídeo.
1.  Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.
1.  Publicar o elemento e obter os URLs de transferência progressiva e transmissão em fluxo.  
1.  Reproduzir os conteúdos.


## Criar uma conta dos Media Services do Azure

1. No [Portal Clássico do Azure](https://manage.windowsazure.com/), clique em **Novo**, em **Serviço de Multimédia** e em **Criação Rápida**.

    ![Criação Rápida dos Media Services](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. Em **NOME**, introduza o nome da nova conta. Um nome de conta dos Media Services é composto por números ou letras minúsculas sem espaços, com 3 a 24 carateres de comprimento.

3. Em **REGIÃO**, selecione a região geográfica que será utilizada para armazenar os registos de metadados da conta dos Media Services. Apenas as regiões dos Media Services disponíveis são apresentadas na caixa de lista pendente.

4. Em **CONTA DO STORAGE**, selecione uma conta do Storage para fornecer o Blob Storage do conteúdo de multimédia da conta de Media Services. Pode selecionar uma conta do Storage existente na mesma região geográfica da conta dos Media Services ou pode criar uma nova conta do Storage. É criada uma nova conta do Storage na mesma região.

5. Se tiver criado uma nova conta do Storage, em **NOVO NOME DA CONTA DO STORAGE**, introduza um nome para a conta do Storage. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.

6. Clique em **Criação Rápida** na parte inferior do formulário.

    Pode monitorizar o estado do processo na área de mensagens na parte inferior da janela.

    Após criar a conta com êxito, o estado é alterado para Ativo.

    Na parte inferior da página, o botão **GERIR CHAVES** aparece. Ao clicar neste botão, é apresentada uma caixa de diálogo com o nome da conta dos Media Services e as chaves primárias e secundárias. Serão necessários o nome da conta e as informações da chave primária para aceder através de programação à conta dos Media Services.

    ![Página Media Services](./media/media-services-portal-get-started/wams-mediaservices-page.png)

    Por predefinição, quando faz duplo clique no nome da conta, a página Início Rápido é apresentada. Esta página permite-lhe realizar algumas tarefas de gestão que também estão disponíveis noutras páginas do portal. Por exemplo, pode carregar um ficheiro de vídeo a partir desta página ou fazê-lo a partir da página CONTEÚDO.


## Configurar o ponto final de transmissão em fluxo utilizando o portal

Ao trabalhar com os Media Services do Azure, uma das situações mais comuns é a distribuição de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Com a transmissão em fluxo de velocidade de transmissão adaptável, o cliente pode mudar para uma velocidade de transmissão superior ou inferior enquanto o vídeo é apresentado, consoante a largura de banda de rede atual, a utilização da CPU, entre outros fatores. Os Media Services suportam as seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), transmissão em Fluxo Uniforme, MPEG DASH e HDS (para detentores de licença do Adobe PrimeTime/acesso apenas).

Os Media Services fornecem um empacotamento dinâmico, permitindo a distribuição do seu conteúdo codificado por Transmissão em Fluxo Uniforme ou MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Media Services (MPEG DASH, HLS, Transmissão em Fluxo Uniforme, HDS) sem ter de voltar a criar o pacote para estes formatos de transmissão em fluxo.

Para tirar partido do empacotamento dinâmico, tem de fazer o seguinte:

- Codificar o ficheiro (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de Transmissão em Fluxo Uniforme de velocidade de transmissão adaptável (os passos da codificação são demonstrados mais à frente neste tutorial).  
- Obter, pelo menos, uma unidade de transmissão em fluxo para o *ponto final de transmissão em fluxo* a partir do qual planeia distribuir o conteúdo.

Com o empacotamento dinâmico só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento, os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

Para alterar o número de unidades reservadas de transmissão em fluxo, faça o seguinte:

1. No [Portal Clássico do Azure](https://manage.windowsazure.com/), clique em **Media Services**. Em seguida, clique no nome do serviço de multimédia.

2. Selecione a página PONTOS FINAIS DE TRANSMISSÃO EM FLUXO. Em seguida, clique no ponto final de transmissão em fluxo que pretende modificar.

3. Para especificar o número de unidades de transmissão em fluxo, selecione o separador **ESCALA** e mova o controlo de deslize de **capacidade de reserva**.

    ![Página Escala](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. Clique no botão **GUARDAR** para guardar as alterações.

    A alocação de quaisquer novas unidades demora cerca de 20 minutos a concluir.

    >[AZURE.NOTE] Atualmente, a passagem de um valor positivo para nenhum das unidades de transmissão em fluxo pode desativar a transmissão em fluxo até perto de uma hora.
    >
    > O maior número de unidades especificadas para o período de 24 horas é utilizado para calcular o custo. Para obter informações sobre os detalhes de preços, consulte [Detalhes de Preços dos Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).

## Carregar conteúdo


1. No [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), clique em **Media Services** e, em seguida, clique no nome da conta de Media Services.
2. Selecione a página CONTEÚDO.
3. Clique no botão **Carregar** na página ou na parte inferior do portal.
4. Na caixa de diálogo **Carregar conteúdo**, procure o ficheiro de elemento pretendido. Clique no ficheiro e, em seguida, em **Abrir** ou prima Enter.

    ![Caixa de diálogo Carregar Conteúdo][uploadcontent]

5. Na caixa de diálogo **Carregar conteúdo**, clique no botão de verificação para aceitar o **Ficheiro** e o **Nome do Conteúdo**.
6. O carregamento é iniciado e pode controlar o progresso na parte inferior do portal.  

    ![Estado da Tarefa][status]

Após concluir o carregamento, verá o novo elemento indicado na lista de Conteúdos. A convenção define que o nome terá “**-Source**” no final para ajudar no controlo do novo conteúdo enquanto conteúdo de origem para as tarefas de codificação.

![Página Conteúdo][contentpage]

Se o valor do tamanho do ficheiro não for atualizado quando o processo de carregamento terminar, selecione o botão **Sincronizar Metadados**. Este processo sincroniza o tamanho do ficheiro de elemento com o tamanho real do ficheiro armazenado e atualiza o valor na página Conteúdo.


## Codificar conteúdo

### Descrição geral

Para distribuir vídeo digital através da Internet, deve comprimir a multimédia. Os Media Services fornecem um codificador de multimédia que permite especificar a forma como pretende codificar o conteúdo (por exemplo, codecs a utilizar, formato do ficheiro, resolução e velocidade de transmissão).

Ao trabalhar com os Media Services do Azure, uma das situações mais comuns é a distribuição de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Com a transmissão em fluxo de velocidade de transmissão adaptável, o cliente pode mudar para uma velocidade de transmissão superior ou inferior enquanto o vídeo é apresentado, consoante a largura de banda de rede atual, a utilização da CPU, entre outros fatores. Os Media Services suportam as seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), transmissão em Fluxo Uniforme, MPEG DASH e HDS (para detentores de licença do Adobe PrimeTime/acesso apenas).

Os Media Services fornecem um empacotamento dinâmico, permitindo a distribuição do conteúdo codificado por Transmissão em Fluxo Uniforme ou MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Media Services (MPEG DASH, HLS, Transmissão em Fluxo Uniforme ou HDS) sem ter de voltar a criar o pacote para estes formatos de transmissão em fluxo.

Para tirar partido do empacotamento dinâmico, tem de fazer o seguinte:

- Codificar o ficheiro (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de Transmissão em Fluxo Uniforme de velocidade de transmissão adaptável (os passos da codificação são demonstrados mais à frente neste tutorial).
- Obter, pelo menos, uma unidade de transmissão em fluxo a pedido para o ponto final de transmissão em fluxo a partir do qual planeia distribuir o seu conteúdo. Para obter mais informações, consulte [Como escalar unidades reservadas para transmissão em fluxo a pedido](media-services-manage-origins.md#scale_streaming_endpoints/).

Com o empacotamento dinâmico só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento, os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

Observe que, além de poder utilizar as capacidades de empacotamento dinâmico, as unidades reservadas para transmissão em fluxo a pedido fornecem uma capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Por predefinição, a transmissão em fluxo a pedido está configurada num modelo de instância partilhada no qual os recursos do servidor (por exemplo, computação ou capacidade de saída) são partilhados com todos os outros utilizadores. Para melhorar o débito de transmissão em fluxo a pedido, é recomendada a compra de unidades reservadas para transmissão em fluxo a pedido.

### Codificar

Esta secção descreve os passos que pode seguir para codificar o conteúdo com o Codificador de Multimédia Standard utilizando o Portal Clássico do Azure.

1.  Selecione o ficheiro que gostaria de codificar.
    Se a codificação for suportada para este tipo de ficheiro, o botão **PROCESSO** na parte inferior da página CONTEÚDO ficará ativado.
4. Na caixa de diálogo **PROCESSO**, selecione o processador **Codificador de Multimédia Standard**.
5. Escolha uma das **configurações de codificação**.

    ![Processo 2][process2]

    O tópico [Cadeias Predefinidas de Tarefas para o Codificador de Multimédia Standard](https://msdn.microsoft.com/en-US/library/mt269960) explica o que cada predefinição significa.  

5. Introduza o nome do conteúdo de saída amigável pretendido ou aceite a predefinição. Em seguida, clique no botão de verificação para iniciar a operação de codificação. Pode controlar o progresso na parte inferior do portal.
6. Selecione **OK**.

    Depois da conclusão da codificação, a página CONTEÚDO conterá o ficheiro codificado.

    Para ver o progresso da tarefa de codificação, mude para a página **TAREFAS**.  

    Se o valor do tamanho do ficheiro não for atualizado depois da conclusão da codificação, selecione o botão **Sincronizar Metadados**. Este processo sincroniza o tamanho do ficheiro de elemento de saída com o tamanho real do ficheiro armazenado e atualiza o valor na página Conteúdo.


## Publicar conteúdo

### Descrição geral

Para fornecer um URL ao utilizador que possa ser utilizado para transmitir ou transferir o conteúdo, primeiro precisa de “publicar” o elemento criando um localizador. Os localizadores fornecem acesso aos ficheiros contidos no elemento. Os Media Services suportam dois tipos de localizadores: localizadores OnDemandOrigin, utilizados para transmitir multimédia (por exemplo, MPEG DASH, HLS ou Transmissão em Fluxo Uniforme) e localizadores de Assinatura de Acesso (SAS), utilizados para transferir ficheiros de multimédia.

Quando utiliza o Portal Clássico do Azure para publicar os elementos, os localizadores são criados para si e obtém um URL baseado em OnDemand (se o elemento possuir um ficheiro .ism) ou um URL SAS.

Um URL SAS tem o seguinte formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Um URL de transmissão em fluxo tem o seguinte formato e pode ser utilizado para reproduzir elementos de Transmissão em Fluxo Uniforme.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para compilar um URL de transmissão em fluxo HLS, acrescente (format=m3u8-aapl) ao URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para compilar um URL de transmissão em fluxo MPEG DASH, acrescente (format=mpd-time-csf) ao URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Os localizadores têm data de expiração. Quando utilizar o portal para publicar os elementos, são criados localizadores com uma data de expiração de 100 anos.

>[AZURE.NOTE] Se utilizou o portal para criar localizadores antes de março de 2015, estes foram criados com uma data de expiração de dois anos.  

Para atualizar uma data de expiração num localizador, utilize as APIs [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Observe que, ao atualizar a data de expiração de um localizador SAS, o URL é alterado.

### Publicar

Para utilizar o portal para publicar um elemento, faça o seguinte:

1. Selecione o elemento.
2. Em seguida, clique no botão publicar.

 ![Conteúdo Publicado][publishedcontent]


## Reproduzir conteúdos a partir do portal

O Portal Clássico do Azure fornece um leitor de conteúdos que pode utilizar para testar o vídeo.

Clique no vídeo pretendido e, em seguida, clique no botão **Reproduzir** na parte inferior do portal.

São aplicáveis algumas considerações:

- Certifique-se de que o vídeo foi publicado.
- O **LEITOR DE CONTEÚDOS DE MEDIA SERVICES** reproduz a partir do ponto final de transmissão em fluxo predefinido. Se pretender reproduzir a partir de um ponto final de transmissão em fluxo não predefinido, utilize outro leitor. Por exemplo, [Leitor de Media Services do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![Leitor AMS][AMSPlayer]



##Passos Seguintes: percursos de aprendizagem dos Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Enviar comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## Está à procura de outra coisa?

Se este tópico não continha o que pretendia, algo está em falta ou não correspondeu de alguma forma às suas necessidades, envie-nos os seus comentários utilizando o thread Disqus abaixo.

### Recursos adicionais
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 – Get your video online now! (em inglês)</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 – Dynamic Packaging and Mobile Devices (em inglês)</a>


<!-- Anchors. -->


<!-- URLs. -->
[Portal Clássico do Azure]: http://manage.windowsazure.com/


<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png



<!--HONumber=Aug16_HO1-->


