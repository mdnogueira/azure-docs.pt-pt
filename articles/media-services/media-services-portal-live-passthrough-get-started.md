<properties 
    pageTitle="Como realizar uma transmissão em fluxo em direto com codificadores no local através do Portal do Azure | Microsoft Azure" 
    description="Este tutorial explica os passos para criar um Canal configurado para uma entrega pass-through." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="06/05/2016" 
    ms.author="juliako"/>


#Como realizar uma transmissão em fluxo em direto com codificadores no local através do Portal do Azure

Este tutorial explica os passos para criar um **Canal** configurado para uma entrega pass-through através do Portal do Azure. 

Os Media Services do Azure no Portal do Azure estão atualmente em pré-visualização.   


##Pré-requisitos

O seguinte é necessário para concluir o tutorial:

- Uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Como Criar uma Conta de Media Services](media-services-create-account.md).
- Uma câmara Web. Por exemplo, [codificador Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm).

É altamente recomendado rever os seguintes artigos:

- [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Descrição geral da Transmissão em Fluxo em Direto através dos Media Services do Azure](media-services-manage-channels-overview.md)
- [Transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo com velocidade de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Cenário comum de transmissão em fluxo em direto

Os passos seguintes descrevem as tarefas envolvidas na criação de aplicações de transmissão em fluxo em direto comuns que utilizam canais que estão configurados para entrega pass-through. Este tutorial mostra como criar e gerir um canal pass-through e eventos em direto.

1. Ligue uma câmara de vídeo a um computador. Iniciar e configurar um codificador em direto no local que produza um RTMP com velocidade de transmissão múltipla ou uma transmissão em fluxo MP4 fragmentada. Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Este passo também pode ser realizado depois de criar o Canal.

1. Criar e iniciar um Canal pass-through.
1. Obtenha o URL de inserção do Canal. 

    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
1. Obtenha o URL de pré-visualização do Canal. 

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.

3. Crie um evento/programa em direto. 

    Quando utilizar o Portal do Azure, ao criar um evento em direto também cria um elemento. 
      
    >[AZURE.NOTE]Certifique-se de que tem, pelo menos, uma unidade reservada para transmissão em fluxo no ponto final da transmissão a partir do qual pretende transmitir o conteúdo.
1. Inicie o evento/programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
2. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
1. Pare o evento/programa sempre que pretender interromper a transmissão em fluxo e arquivar o evento.
1. Elimine o evento/programa (e, opcionalmente, elimine o elemento).     

>[AZURE.IMPORTANT] Reveja a [Transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo com velocidade de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md) para saber mais sobre os conceitos e as considerações relacionadas com a transmissão em fluxo em direto com codificadores no local e canais pass-through.

##Para ver as notificações e erros

Se pretende ver as notificações e os erros produzidos pelo Portal do Azure, clique no ícone de Notificação.

![Notificações](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##Configurar os pontos finais de transmissão em fluxo 

Os Media Services fornecem um empacotamento dinâmico, permitindo a entrega dos seus MP4s com várias velocidades nos seguintes formatos de transmissão em fluxo: MPEG DASH, HLS, Transmissão em Fluxo Uniforme ou HDS, sem ter de voltar a criar o pacote para estes formatos de transmissão em fluxo. Com o empacotamento dinâmico só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento, os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

Para tirar partido do empacotamento dinâmico, precisa de, pelo menos, uma unidade de transmissão em fluxo para o ponto final de transmissão em fluxo a partir do qual planeia distribuir o conteúdo.  

Para criar e alterar o número de unidades reservadas para transmissão em fluxo, faça o seguinte:

1. Na janela **Definições**, clique em **Pontos finais de transmissão em fluxo**. 

2. Clique no ponto final de transmissão em fluxo predefinido. 

    A janela **DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO** é apresentada.

3. Para especificar o número de unidades de transmissão em fluxo, deslize o controlo de deslize **Unidades de transmissão em fluxo**.

    ![Unidades de transmissão em fluxo](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Clique no botão **Guardar** para guardar as alterações.

    >[AZURE.NOTE]A alocação de quaisquer novas unidades pode demorar até 20 minutos a concluir.
    
##Criar e iniciar eventos e canais pass-through

Um canal está associado a eventos/programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Os canais gerem eventos. 
    
Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. A duração da janela de arquivo dita também o tempo máximo que os clientes podem recuar a partir da posição atual em direto. Os eventos podem ser executados durante o período de tempo especificado, contudo, o conteúdo que não respeitar essa duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada evento está associado a um elemento. Para publicar o evento, deve criar um localizador OnDemand para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três eventos em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar os eventos em direto existentes. Em vez disso, crie e inicie um novo evento para cada evento.

Inicie o evento quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o evento e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado por um evento; o evento deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o evento, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

###Utilizar o portal para criar um canal 

Estas secções mostram como utilizar a opção **Criação Rápida** para criar um canal pass-through.

Para obter mais detalhes sobre canais pass-through, consulte [Transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo com velocidade de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md).

1. Na janela **Definições**, clique em **Transmissão em Fluxo em Direto**. 

    ![Introdução](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    A janela **Transmissão em Fluxo em Direto** é apresentada.

3. Clique em **Criação Rápida** para criar um canal pass-through com o protocolo de inserção RTMP.

    A janela **CRIAR UM NOVO CANAL** é apresentada.
4. Dê um nome ao novo canal e clique em **Criar**. 

    Tal irá criar um canal pass-through com o protocolo de inserção RTMP.

    O canal também adiciona, inicia e publica um evento/programa em direto predefinido. Este evento está configurado para ter 8 horas de janela de arquivo. 

    Para adicionar mais eventos, prima o botão **Evento em Direto**.

##Obter URLs de inserção

Assim que o canal seja criado, pode obter os URLs de inserção que fornecerá ao codificador em direto. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

![Criado](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##Ver um evento

Para ver o evento, clique em **Ver** no Portal do Azure ou copie o URL de transmissão em fluxo e utilize um leitor da sua preferência. 
 
![Criado](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

O evento em direto será convertido automaticamente para conteúdo a pedido quando parado.

##Limpeza

Para obter mais detalhes sobre canais pass-through, consulte [Transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo com velocidade de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md).

- Um canal pode ser parado apenas quando todos os eventos/programas no canal foram parados.  Assim que o Canal esteja parado, não será cobrado qualquer custo. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
- Um canal pode ser eliminado apenas quando todos os eventos em direto canal tiverem sido eliminados.

##Ver conteúdo arquivado

Mesmo depois de parar e eliminar o evento, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não elimine o elemento. Não é possível eliminar um elemento se este é utilizado por um evento; o evento deve ser eliminado primeiro. 

Para gerir os seus elementos, selecione **Definição** e clique em **Elementos**.

![Elementos](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##Percursos de aprendizagem dos Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Enviar comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Jun16_HO2-->


