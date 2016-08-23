<properties 
    pageTitle="Como realizar uma transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla com o Portal Clássico do Azure" 
    description="Este tutorial explica-lhe os passos da criação de um canal que recebe uma transmissão em fluxo em direto com uma velocidade de transmissão única e a codifica para uma transmissão em fluxo com velocidade de transmissão múltipla utilizando o Portal Clássico do Azure" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako,anilmur" 
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


#Como realizar uma transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla com o Portal Clássico do Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Este tutorial explica-lhe os passos da criação de um **Canal** que recebe uma transmissão em fluxo em direto com uma velocidade de transmissão única e a codifica para uma transmissão com velocidade de transmissão múltipla.

>[AZURE.NOTE]Para obter mais informações conceptuais relacionadas com Canais ativados para Live Encoding, consulte [Transmissão em fluxo em direto através dos Media Services do Azure para criar transmissões com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).

##Cenário Comum de Transmissão em Fluxo em Direto

Os seguintes são passos gerais referentes à criação de aplicações comuns de transmissão em fluxo em direto.

>[AZURE.NOTE] Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte amslived através de Microsoft.com, se tiver de executar um Canal durante períodos de tempo mais longos.

1. Ligue uma câmara de vídeo a um computador. Inicie e configure um codificador em direto no local que possa enviar uma transmissão de velocidade de transmissão única através de um dos seguintes protocolos: RTMP, Transmissão em Fluxo Uniforme ou RTP (MPEG-TS). Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Este passo também pode ser realizado depois de criar o Canal.

1. Crie e inicie um Canal. 

1. Obtenha o URL de inserção do Canal. 

    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
1. Obtenha o URL de pré-visualização do Canal. 

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.

3. Crie um programa (que também irá criar um elemento). 
1. Publique o programa (que criará um localizador OnDemand para o elemento associado).  

    Certifique-se de que tem, pelo menos, uma unidade reservada para transmissão em fluxo no ponto final da transmissão a partir do qual pretende transmitir o conteúdo.
1. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
2. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
1. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.
1. Elimine o Programa (e, opcionalmente, elimine o elemento).   

##Neste tutorial

Neste tutorial, o Portal Clássico do Azure é utilizado para realizar as seguintes tarefas: 

2.  Configurar os pontos finais de transmissão em fluxo.
3.  Crie um canal ativado para realizar live encoding.
1.  Obtenha o URL de Inserção para o fornecer ao codificador em direto. O codificador em direto irá utilizar este URL para inserir a transmissão em fluxo no Canal. .
1.  Crie um programa (e um elemento)
1.  Publique o elemento e obtenha os URLs de transmissão em fluxo  
1.  Reproduza o seu conteúdo 
2.  Limpeza

##Pré-requisitos
O seguinte é necessário para concluir o tutorial.

- Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Criar Conta](media-services-create-account.md).
- Uma câmara Web e um codificador que possa enviar uma transmissão de velocidade de transmissão única.

##Configurar o ponto final de transmissão em fluxo utilizando o Portal

Ao trabalhar com os Media Services do Azure, uma das situações mais comuns é a distribuição de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Com a transmissão em fluxo de velocidade de transmissão adaptável, o cliente pode mudar para uma velocidade de transmissão superior ou inferior enquanto o vídeo é apresentado, consoante a largura de banda de rede atual, a utilização da CPU, entre outros fatores. Os Media Services suportam as seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), transmissão em Fluxo Uniforme, MPEG DASH e HDS (para detentores de licença do Adobe PrimeTime/acesso apenas). 

Ao trabalhar com transmissão em fluxo em direto, um codificador em direto no local (no nosso caso, Wirecast) insere uma transmissão em fluxo em direto com velocidade de transmissão múltipla no seu canal. Quando a transmissão em fluxo é solicitada por um utilizador, os Media Services utilizam o empacotamento dinâmico para voltar a criar o pacote da transmissão em fluxo de origem na transmissão em fluxo de velocidade de transmissão adaptável solicitada (HLS, TRAÇO ou Uniforme). 

Para tirar partido do empacotamento dinâmico, precisa de, pelo menos, uma unidade de transmissão em fluxo para o **ponto final de transmissão em fluxo** a partir do qual planeia distribuir o conteúdo.

Para alterar o número de unidades reservadas de transmissão em fluxo, faça o seguinte:

1. No [Portal Clássico do Azure](https://manage.windowsazure.com/), clique em **Media Services**. Em seguida, clique no nome do serviço de multimédia.

2. Selecione a página PONTOS FINAIS DE TRANSMISSÃO EM FLUXO. Em seguida, clique no ponto final de transmissão em fluxo que pretende modificar.

3. Para especificar o número de unidades de transmissão em fluxo, selecione o separador ESCALA e mova o **controlo de deslize de capacidade de reserva**.

    ![Página Escala](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-origin-scale.png)

4. Prima o botão GUARDAR para guardar as alterações.

    A alocação de quaisquer novas unidades demora cerca de 20 minutos a concluir. 

     
    >[AZURE.NOTE] Atualmente, a passagem de um valor positivo para nenhum das unidades de transmissão em fluxo pode desativar a transmissão em fluxo até perto de uma hora.
    >
    > O maior número de unidades especificadas para o período de 24 horas é utilizado para calcular o custo. Para obter informações sobre os detalhes de preços, consulte [Detalhes de Preços dos Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).

 
##Criar um CANAL

1.  No [Portal Clássico do Azure](http://manage.windowsazure.com/), clique em Media Services e, em seguida, clique no nome da conta de Media Services.
2.  Selecione a página CANAIS.
3.  Selecione Adicionar+ para adicionar um novo canal.

Escolha tipos de codificação **Standard**. Este tipo especifica que pretende criar um Canal ativado para live encoding. Isso significa que a transmissão em fluxo de velocidade de transmissão de entrada é enviada para o Canal e codificada como uma transmissão em fluxo com velocidade de transmissão múltipla através de configurações específicas de codificador em direto. Para obter mais informações, consulte [Transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).

![standard0][standard0]

Para o tipo de codificação **Standard**, temos como opções válidas de protocolo:

- MP4 fragmentado de velocidade de transmissão única (Transmissão em Fluxo Uniforme)
- RTMP de velocidade transmissão única
- RTP (MPEG-TS): Transmissão de Fluxo de Transporte de MPEG-2 sobre RTP.

Para uma explicação mais detalhada sobre cada protocolo, consulte [Transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).

![standard1][standard1]

Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.  

Na página **Configuração de Publicidade**, pode especificar a origem dos sinais de marcadores de publicidade. Quando utilizar o Portal, pode selecionar apenas a API, o que indica que o codificador em direto no Canal deve estar à escuta de uma API de Marcador de Publicidade assíncrona. Quando utilizar o Portal, pode selecionar apenas a API.

Para obter mais informações, consulte [Transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).

![standard2][standard2]

Na página **Predefinição de Codificação**, pode selecionar predefinições do sistema. Atualmente, a única predefinição do sistema que pode selecionar é **720p Padrão**.

![standard3][standard3]

Na página **Criação de Canal**, pode definir os endereços IP que estão autorizados a publicar vídeo neste canal. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo,  "10.0.0.1"), um intervalo IP com um endereço IP e uma máscara sub-rede CIDR (por exemplo "10.0.0.1/22") ou um intervalo IP com um endereço IP e uma máscara sub-rede de ponto decimal (por exemplo, "10.0.0.1(255.255.252.0)").

Se não for especificado qualquer endereço IP e não existir nenhuma definição de regra, então, não será permitido qualquer endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.


![standard4][standard4]

>[AZURE.NOTE] Atualmente, o arranque do Canal pode demorar até 30 minutos. A reposição do canal pode demorar até 5 minutos.

Depois de ter criado o Canal, pode selecionar o separador **CODIFICADOR** onde pode ver as configurações dos canais. Também pode gerir anúncios e imagens fixas. 

![standard5][standard5]

Para obter mais informações, consulte [Transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).


##Obter URLs de inserção

Assim que o canal seja criado, pode obter os URLs de inserção que fornecerá ao codificador em direto. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

![readychannel](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ready-channel.png)


![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##Criar e gerir um programa

###Descrição geral

Um canal está associado a programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação entre o Canal e o Programa é muito semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. A duração da janela de arquivo dita também o tempo máximo que os clientes podem recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado um Elemento. Para publicar o programa, deve criar um localizador OnDemand para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento.

Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado por um programa; o programa deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o programa, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido até que elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

###Criar/Iniciar/Parar Programas

Assim que a transmissão em fluxo esteja a ser enviada para o Canal, pode começar o evento de transmissão em fluxo através da criação de um Elemento, Programa e Localizador de Transmissão em Fluxo. Isto irá arquivar a transmissão em fluxo e torná-la disponível para os espetadores através do Ponto Final de Transmissão em Fluxo. 

Existem duas formas de iniciar o evento: 

1. Na página **CANAL**, prima **ADICIONAR** para adicionar um novo programa.

    Especifique: nome do programa, nome do elemento, janela de arquivo e opção de encriptação.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Se marcou **Publicar este programa agora**, os URLs de PUBLICAÇÃO serão criados.
    
    Pode premir **INICIAR**, sempre que estiver pronto para transmitir o programa.

    Assim que iniciar o programa, pode premir REPRODUZIR para começar a reproduzir o conteúdo.


    ![createdprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-created-program.png)

2. Em alternativa, pode utilizar um atalho e premir o botão **INICIAR TRANSMISSÃO EM FLUXO** na página **CANAL**. Isto irá criar um Elemento, Programa e Localizador de Transmissão em Fluxo.

    O programa é denominado DefaultProgram e a janela de arquivo está definida para 1 hora.

    Pode reproduzir o programa publicado a partir da página CANAL. 

    ![channelpublish](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-channel-play.png)


Se clicar em **PARAR TRANSMISSÃO EM FLUXO** na página **CANAL**, o programa predefinido será parado e eliminado. O elemento continuará lá e pode publicá-lo ou retirá-lo da página **CONTEÚDO**.

Se mudar para a página **CONTEÚDO**, verá os elementos que foram criados para os seus programas.

![contentasset](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-content-assets.png)


##Reproduzir conteúdo

Para fornecer ao utilizador um URL que possa ser utilizado para transmitir o seu conteúdo, terá primeiro de "publicar" o seu elemento (tal como foi descrito na secção anterior), criando um localizador (quando publica um elemento com o Portal, os localizadores são criados por si). Os localizadores fornecem acesso aos ficheiros contidos no elemento. 

Dependendo do protocolo de transmissão em fluxo que pretende utilizar para reproduzir o conteúdo, poderá ter de modificar o URL que obtém a partir da ligação **PUBLICAR URL** do canal\programa.

O empacotamento dinâmico tratará do empacotamento da transmissão em fluxo em direto para o protocolo especificado. 

Por predefinição, um URL de transmissão em fluxo tem o seguinte formato e pode ser utilizado para reproduzir elementos de Transmissão em Fluxo Uniforme:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para compilar um URL de transmissão em fluxo HLS, acrescente (format=m3u8-aapl) ao URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para compilar um URL de transmissão em fluxo MPEG DASH, acrescente (format=mpd-time-csf) ao URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Para obter mais informações sobre a distribuição dos seus conteúdos, consulte [Fornecimento de conteúdo](media-services-deliver-content-overview.md).

Pode reproduzir a Transmissão em Fluxo Uniforme através do [Leitor AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) ou de dispositivos iOS e Android para reproduzir HLS versão 3.

##Limpeza

Se terminar a transmissão em fluxo de eventos e pretende limpar os recursos aprovisionados anteriormente, siga o procedimento seguinte.

- Termine o envio da transmissão em fluxo do codificador.
- Pare o canal. Assim que o Canal esteja parado, não será cobrado qualquer custo. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
- Pode parar o seu Ponto Final de Transmissão em Fluxo, a menos que pretenda continuar a fornecer o arquivo do seu evento em direto como uma transmissão em fluxo a pedido. Se o canal estiver num estado parado, não será cobrado qualquer custo.
  

##Considerações

- Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte amslived através de Microsoft.com se tiver de executar um Canal durante períodos de tempo mais longos.
- Certifique-se de que tem, pelo menos, uma unidade reservada para transmissão em fluxo no ponto final da transmissão a partir do qual pretende transmitir o conteúdo.


##Percursos de aprendizagem dos Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Enviar comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



[standard0]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard0.png
[standard1]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard1.png
[standard2]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard2.png
[standard3]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard3.png
[standard4]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard4.png
[standard5]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard_encode.png 


<!--HONumber=Aug16_HO1-->


