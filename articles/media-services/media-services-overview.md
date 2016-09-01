<properties 
    pageTitle="Descrição Geral e Cenários Comuns dos Media Services do Azure" 
    description="Este tópico fornece uma descrição geral dos Media Services do Azure" 
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
    ms.topic="hero-article" 
    ms.date="08/07/2016"
    ms.author="juliako;anilmur"/>

#Descrição Geral e Cenários Comuns dos Media Services do Azure

Os Media Services do Microsoft Azure são uma plataforma baseado na nuvem extensível que permite aos programadores compilar uma gestão de multimédia dimensionável e aplicações de entrega. Os Media Services são baseados nas APIs REST, que permitem carregar, armazenar, codificar e empacotar de forma segura conteúdos de vídeo ou áudio, para o envio de transmissão em fluxo tanto a pedido como em direto para vários clientes (por exemplo, TV, PC e dispositivos móveis).

Pode compilar fluxos de trabalho ponto-a-ponto utilizando inteiramente os Media Services. Pode também optar por utilizar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codificar utilizando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue através dos Media Services.

Pode também optar por transmitir os seus conteúdos em direto ou enviar conteúdos a pedido. Este tópico mostra cenários comuns para a entrega dos seus conteúdos [em direto](media-services-overview.md#live_scenarios) ou [a pedido](media-services-overview.md#vod_scenarios). O tópico também está ligado a outros tópicos relevantes.

## SDKs e ferramentas 

Para compilar soluções de Media Services, pode utilizar:

- [API REST dos Media Services](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Um dos SDKs do Cliente disponíveis: 
    - [SDK do .NET dos Media Services do Azure](https://github.com/Azure/azure-sdk-for-media-services), 
    - [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java), 
    - [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php), 
    - [Media Services do Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Esta é uma versão não Microsoft de um SDK Node.js. Esta é mantida por uma comunidade e não tem atualmente uma cobertura de 100% das APIs do AMS). 
- Ferramentas existentes: 
    - [Portal Clássico do Azure](http://manage.windowsazure.com/) 
    - [Explorador de Media Services do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE – Azure Media Services Explorer, uma aplicação Winforms/C# para Windows)

##Percursos de aprendizagem dos Media Services

Pode ver percurso de aprendizagem do AMS aqui:

- [Fluxo de Trabalho de Transmissão em Fluxo em Direto do AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de Trabalho de Transmissão em Fluxo a Pedido do AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Pré-requisitos

Para começar a utilizar os Media Services do Azure, deve ter o seguinte:
 
3. Uma conta do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com).
2. Uma conta de Media Services do Azure Utilize o Portal Clássico do Azure, .NET ou a API REST para criar uma conta dos Media Services do Azure. Para obter mais informações, consulte [Criar Conta](media-services-create-account.md).
3. (Opcional) Configurar o ambiente de desenvolvimento. Escolha .NET ou a API REST para o ambiente de desenvolvimento. Para obter mais informações, consulte [Configurar o ambiente](media-services-dotnet-how-to-use.md). 

    Além disso, saiba como se ligar através de programação [Ligar](media-services-dotnet-connect-programmatically.md).
4. (Recomendado) Atribua uma ou mais unidades de escala. É recomendado alocar uma ou mais unidades de escala para as aplicações no ambiente de produção.   Para obter mais informações, consulte [Gerir pontos finais de transmissão em fluxo](media-services-manage-origins.md).

##Conceitos e descrição geral

Para saber mais sobre os conceitos de Media Services do Azure, consulte [Conceitos](media-services-concepts.md).

Para obter um conjunto de procedimentos que apresente todos os componentes principais dos Media Services do Azure, consulte [Tutorial Passo-a-Passo dos Media Services do Azure](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Este conjunto possui uma excelente descrição geral dos conceitos e utiliza a ferramenta AMSE para demonstrar as tarefas AMS. Tenha em atenção que a ferramenta AMSE é uma ferramenta do Windows. Esta ferramenta suporta a maioria das tarefas que pode realizar através de programação com [o SDK do AMS para .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java) ou [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>Fornecer Multimédia a Pedido com Media Services do Azure: cenários e tarefas comuns

Esta secção descreve cenários comuns e fornece ligações para tópicos relevantes. O diagrama seguinte mostra as principais partes da plataforma dos Media Services envolvidas na distribuição de conteúdos a pedido. 

![Fluxo de trabalho do VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###Proteger conteúdos em armazenamento e distribuir multimédia de transmissão em fluxo em segurança (não encriptadas)

1. Carregue um ficheiro mezanino de alta qualidade para um elemento.
    
    É recomendado aplicar uma opção de encriptação de armazenamento ao seu elemento para proteger os seus conteúdos durante o carregamento e enquanto estes permanecem inativos em armazenamento.
 
1. Codifique para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável. 

    É recomendado aplicar uma opção de encriptação de armazenamento ao elemento de saída para proteger os seus conteúdos enquanto estes permanecem.
    
1. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico). 
    
    Se o seu elemento estiver armazenamento encriptado, **deve** configurar a política de distribuição do elemento. 

1. Publique o elemento através da criação de um localizador OnDemand.

    Certifique-se de que tem, pelo menos, uma unidade reservada para transmissão em fluxo no ponto final da transmissão a partir do qual pretende transmitir o conteúdo.

1. Transmita os conteúdos publicados.

###Proteger os conteúdos em armazenamento, distribuir multimédia de transmissão em fluxo encriptada de forma dinâmica  

Para poder utilizar a encriptação dinâmica, primeiro tem de obter, pelo menos, uma unidade reservada para transmissão em fluxo no ponto final da transmissão em fluxo a partir da qual pretende transmitir conteúdos encriptados.

1. Carregue um ficheiro mezanino de alta qualidade para um elemento. Aplique a opção de encriptação de armazenamento ao elemento.
1. Codifique para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável. Aplique a opção de encriptação de armazenamento ao elemento de saída.
1. Crie uma chave de conteúdo de encriptação para o elemento que pretende que seja encriptado dinamicamente durante a reprodução.
2. Configure a política de autorização da chave de conteúdo.
1. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico e a encriptação dinâmica).
1. Publique o elemento através da criação de um localizador OnDemand.
1. Transmita os conteúdos publicados. 

###Utilize a Análise de Multimédia para derivar conhecimentos acionáveis dos seus vídeos 

A Análise de Multimédia é um conjunto de componentes de voz e visão que facilitam a derivação de conhecimentos acionáveis das organizações e empresas dos seus respetivos ficheiros de vídeo. Para obter mais informações, consulte [Descrição Geral da Análise dos Media Services do Azure](media-services-analytics-overview.md).

1. Carregue um ficheiro mezanino de alta qualidade para um elemento.
2. Utilize um dos seguintes serviços de Análise de Multimédia para processar os seus vídeos:
    
    - **Indexador** – [Processa vídeos com o Media Indexer do Azure 2](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** – [Hyperlapse de Arquivos de Multimédia com Hyperlapse de Multimédia do Azure](media-services-hyperlapse-content.md)
    - **Deteção de movimento** – [Deteção de movimento para Análise de Multimédia do Azure](media-services-motion-detection.md).
    - **Deteção de rostos e emoções do rosto** – [Deteção de emoções e rostos para a Análise de Multimédia do Azure](media-services-face-and-emotion-detection.md).
    - **Resumo do vídeo** – [Utilize as Miniaturas de Vídeo de Multimédia do Azure para Criar um Resumo de Vídeo](media-services-video-summarization.md)
3. Os processadores de multimédia de Análise de Multimédia produzem ficheiros MP4 ou ficheiros JSON. Se um processador de multimédia produzir um ficheiro MP4, pode transferir progressivamente o ficheiro. Se um processador de multimédia produzir um ficheiro JSON, pode transferir o ficheiro a partir do Blob Storage do Azure. 


###Fornecer transferência progressiva 

1. Carregue um ficheiro mezanino de alta qualidade para um elemento.
1. Codifique para um único ficheiro MP4.
1. Publique o elemento através da criação de um localizador OnDemand ou SAS.

    Se utilizar o localizador OnDemand, certifique-se de que tem, pelo menos, uma unidade reservada para transmissão em fluxo no ponto final de transmissão em fluxo a partir da qual planeia transferir progressivamente os conteúdos.

    Se utilizar o localizador SAS, o conteúdo é transferido a partir do Blob Storage do Azure. Neste caso, não é necessário ter unidades reservadas para transmissão em fluxo.
  
1. Transferir progressivamente os conteúdos.

##<a id="live_scenarios"></a>Distribuir Eventos em Direto com Media Services do Azure

Ao trabalhar com Transmissão em Fluxo em Direto, os seguintes componentes estão normalmente relacionados:

- Uma câmara, que é utilizada para difundir um evento.
- Um codificador vídeo em direto que converte sinais da câmara para transmissões em fluxos que são enviadas para um serviço de transmissão em fluxo em direto.

    Opcionalmente, vários codificadores com sincronização de hora em direto. Para determinados eventos críticos em direto que exigem uma enorme disponibilidade e qualidade da experiência, é recomendado utilizar codificadores ativo-ativo com sincronização de hora para obter uma ativação pós-falha totalmente integrada sem perda de dados.
- Um serviço de transmissão em fluxo em direto que permite realizar o seguinte:
    
    - inserir conteúdos em direto utilizando vários protocolos de transmissão em fluxo em direto (por exemplo, RTMP ou Transmissão em Fluxo Uniforme)
    - (opcionalmente) codificação da sua transmissão para uma transmissão em fluxo de velocidade de transmissão adaptável
    - pré-visualizar a sua transmissão em fluxo em direto
    - gravar e armazenar os conteúdos inseridos de forma a transmiti-los em fluxo posteriormente (Vídeo a Pedido)
    - distribuir os conteúdos através de protocolos de transmissão em fluxo comuns (por exemplo, MPEG DASH, Uniforme, HLS, HDS) diretamente para os seus clientes ou para uma Rede de Entrega de Conteúdos (CDN) para uma maior distribuição.


Os **Media Services do Microsoft Azure** (AMS) fornecem a capacidade de inserir, codificar, pré-visualizar, armazenar e distribuir os seus conteúdos por transmissão em fluxo em direto.

Quando distribui os seus conteúdos aos seus clientes, o seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para obter as condições de qualidade e de rede, utilize codificadores em direto para codificar a sua transmissão para uma transmissão em fluxo de velocidade de transmissão múltipla (velocidade de transmissão adaptável).  Para realizar a transmissão em fluxo para dispositivos diferentes, utilize o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos Media Services para empacotar novamente e de forma dinâmica a sua transmissão em fluxo para protocolos diferentes. Os Media Services suportam a distribuição das seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), transmissão em Fluxo Uniforme, MPEG DASH e HDS (para detentores de licença do Adobe PrimeTime/acesso apenas).

Nos Media Services do Azure, os **Canais**, **Programas** e **Pontos Finais de Transmissão em Fluxo** lidam com todas as funcionalidades de transmissão em fluxo em direto, incluindo inserção, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. Um Canal pode receber transmissões em fluxo de entrada em direto das seguintes formas:

- Um codificador em direto no local envia um **RTMP** ou uma **Transmissão em Fluxo Uniforme** com velocidade de transmissão múltipla (MP4 fragmentado) para o Canal configurado para distribuição **pass-through**. A distribuição **pass-through** ocorre quando as transmissões em fluxo inseridas passam pelos **Canais** sem qualquer processamento adicional. Pode utilizar os seguintes codificadores em direto que transmitam uma Transmissão em Fluxo Uniforme com velocidade de transmissão múltipla: Elementar, Envivio, Cisco.  Os seguintes codificadores em direito transmitem RTMP: transcodificadores Tricaster, Telestream Wirecast e Adobe Flash Live.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

    >[AZURE.NOTE] A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](/pricing/details/media-services/).
    
- Um codificador em direto no local envia uma transmissão fluxo de velocidade única para o Canal ativado para realizar live encoding com os Media Services num dos seguintes formatos: RTP (MPEG-TS), RTMP ou Transmissão em Fluxo Uniforme (MP4 fragmentado). O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.


###Trabalhar com Canais que recebem transmissões em fluxo em direto com velocidade de transmissão múltipla a partir de codificadores no local (pass-through)

O diagrama seguinte mostra as principais partes da plataforma de AMS envolvidas no fluxo de trabalho de **pass-through**.

![Fluxo de trabalho em direto][live-overview2]

Para obter mais informações, consulte [Trabalhar com Canais que recebem transmissões em Fluxo em Direto de Múltipla Velocidade de Transmissão a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md).

###Trabalhar com Canais ativados para realizar live encoding com Media Services do Azure

O diagrama seguinte mostra as partes principais da plataforma do AMS envolvidas no fluxo de trabalho de Transmissão em Fluxo em Direto onde um Canal é ativado para realizar live encoding comMedia Services.

![Fluxo de trabalho em direto][live-overview1]

Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).


##Consumo de conteúdos

Os Media Services do Azure fornecem as ferramentas necessárias para criar aplicações avançadas e dinâmicas de leitor de cliente para a maioria das plataformas, incluindo: Dispositivos iOS dispositivos, Dispositivos Android, Windows, Windows Phone, Xbox e Caixas descodificadoras. O tópico seguinte fornece ligações para SDKs e Estruturas do Leitor que pode utilizar para desenvolver as suas próprias aplicações de cliente que podem consumir multimédia de transmissão de fluxo a partir dos Media Services.

[Desenvolver Aplicações do Leitor de Vídeo](media-services-develop-video-players.md)

##Ativar a CDN do Azure

Os Media Services suportam a integração com a CDN do Azure. Para obter mais informações sobre como ativar a CDN do Azure, consulte [Como Gerir Transmissão em Fluxo de Pontos Finais numa Conta de Media Services](media-services-manage-origins.md#enable_cdn).

##Escalar uma conta dos Media Services

Pode escalar **Media Services** especificando o número das **Unidades Reservadas para Transmissão em Fluxo** e das **Unidades de Codificação Reservadas** que pretende aprovisionar na sua conta.

Também pode escalar a sua conta dos Media Services adicionando-lhe contas deo Storage. Cada conta do Storage está limitada a 500 TB. Para expandir o limite predefinido do seu armazenamento, pode optar por ligar várias contas do Storage numa única conta de Media Services.

[Este](media-services-how-to-scale.md) tópico está ligado a tópicos relevantes.

##Suporte

O [Suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte para o Azure, incluindo os Media Services.

##Enviar comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Contrato de Nível de Serviço (SLA)

- Para Media Services Encoding, podemos garantir 99,9% de disponibilidade das transações da API REST.
- Para a Transmissão em Fluxo, iremos responder com êxito às solicitações com 99,9% de disponibilidade garantida para os conteúdos de multimédia existentes quando, pelo menos, uma Unidade de Transmissão em Fluxo for adquirida.
- Para Canais em Direto, garantimos que a execução dos Canais terá uma conectividade externa em, pelo menos, 99,9% das vezes.
- Para Content Protection, garantimos que iremos satisfazer com êxito os pedidos-chave em, pelo menos, 99,9% das vezes.
- Para o Indexador, iremos satisfazer com êxito os pedidos de Tarefa do Indexador com uma Unidade de Codificação Reservada em, pelo menos, 99,9% das vezes.

Para obter mais informações, consulte [SLA do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[descrição geral]: ./media/media-services-overview/media-services-overview.png
[Descrição geral do vod]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 



<!--HONumber=ago16_HO4-->


