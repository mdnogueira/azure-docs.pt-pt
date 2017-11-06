---
title: "Tutoriais do fluxo de trabalho do Avanced multimédia codificador Premium"
description: "Este documento contém instruções mostram como efetuar tarefas avançadas com o fluxo de trabalho do suporte de dados codificador Premium e também como criar fluxos de trabalho complexos com o estruturador de fluxo de trabalho."
services: media-services
documentationcenter: 
author: xstof
manager: cfowler
editor: 
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 565497bd5a35e3c4d69d29512307cf3ca2364bdd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Tutoriais de fluxo de trabalho do suporte de dados codificador Premium avançadas
## <a name="overview"></a>Descrição geral
Este documento contém instruções mostram como personalizar fluxos de trabalho com **estruturador de fluxo de trabalho**. Pode encontrar os ficheiros de fluxo de trabalho real [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TOC
São abrangidos os seguintes tópicos:

* [Codificação MXF para uma MP4 de velocidade de transmissão única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Iniciar um novo fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Utilizar a entrada de ficheiro do suporte de dados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [A inspecionar os fluxos de suporte de dados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Um codificador vídeo para a adicionar. Geração de ficheiros MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Codificação de sequência de áudio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexação fluxos de áudio e vídeo para um contentor de MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Escrever o ficheiro MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Criar um recurso de serviços de suporte de dados a partir do ficheiro de saída](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testar o fluxo de trabalho concluído localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Codificação MXF para multibitrate MP4s - empacotamento dinâmico ativado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Adicionar um ou mais saídas MP4 adicionais](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Configurar os nomes de saída do ficheiro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Adicionar um registo de áudio separada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Adicionar o. ISM SMIL ficheiro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Codificação MXF para multibitrate MP4 - blueprint avançada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Descrição geral do fluxo de trabalho para melhorar a](#workflow-overview-to-enhance)
  * [As convenções de nomenclatura de ficheiros](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publicação de propriedades do componente para a raiz de fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Ter gerado o ficheiro de saída nomes baseiam-se nos valores de propriedade publicada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [A adição de miniaturas para multibitrate MP4 saída](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Descrição geral do fluxo de trabalho para adicionar as miniaturas para](#workflow-overview-to-add-thumbnails-to)
  * [Adicionar JPG codificação](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Lidar com a conversão de espaço de cor](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Escrever miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Detetar erros num fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Corte de baseados no tempo de mensagens em fila de saída multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Descrição geral do fluxo de trabalho para começar a adicionar corte para](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Utilizar o Trimmer de fluxo](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introduzir o componente de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Script num fluxo de trabalho: Olá, mundo](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Com base no período de corte de saída multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Descrição geral de blueprint para começar a adicionar corte para](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Utilizando a lista de Clip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modificar a lista de clip de um componente convertidos em script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Adicionar uma propriedade de conveniência ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Codificação MXF para uma MP4 de velocidade de transmissão única
Esta explicação passo a passo, vamos criar uma velocidade de transmissão única. Ficheiro MP4 com AAC Putador codificado áudio de um. Ficheiro de entrada MXF.

### <a id="MXF_to_MP4_start_new"></a>Iniciar um novo fluxo de trabalho
Abra o estruturador de fluxo de trabalho e selecione "Blueprint de transcodificar o ficheiro"-"nova área de trabalho"-""

O novo fluxo de trabalho irá mostrar 3 elementos:

* Ficheiro de origem principal
* Lista de clip XML
* Ficheiro/elemento de saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Novo fluxo de trabalho de codificação*

### <a id="MXF_to_MP4_with_file_input"></a>Utilizar a entrada de ficheiro do suporte de dados
Para aceitar o nosso ficheiro de suporte de dados de entrada, um começa com a adição de um componente de entrada de ficheiro do suporte de dados. Para adicionar um componente para o fluxo de trabalho, procure na caixa de pesquisa de repositório e arraste a entrada pretendida para o painel do estruturador. Fazê-lo para a entrada de ficheiro do suporte de dados e ligar o componente de ficheiro de origem principal para o pin de entrada de nome de ficheiro a partir da entrada de ficheiro do suporte de dados.

![Entrada de ficheiros de multimédia ligados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada de ficheiros de multimédia ligados*

Antes de podermos fazer muito mais, precisamos primeiro indicar ao estruturador de fluxo de trabalho que gostaríamos de utilizar para estruturar a nossa fluxo de trabalho com um ficheiro de exemplo. Para tal, clique o fundo do estruturador de painel e procure a propriedade de ficheiro de origem principal no painel direito da propriedade. Clique no ícone de pastas e selecione o ficheiro pretendido para testar o fluxo de trabalho com. Assim que este procedimento é efetuado, o componente de entrada de ficheiro do suporte de dados irá inspecionar o ficheiro e preencher os pins de saída para refletir o ficheiro-inspecionado.

![Entrada do ficheiro de multimédia preenchidas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada do ficheiro de multimédia preenchidas*

Enquanto esta ação Especifica com que entrada gostaríamos de trabalhar com, não indica ainda onde a saída codificada deve passar para. Semelhante à forma como o ficheiro de origem principal foi configurado, agora configurar a propriedade da variável de pasta de saída, imediatamente abaixo do mesmo.

![Propriedades de entrada e saída configuradas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriedades de entrada e saída configuradas*

### <a id="MXF_to_MP4_streams"></a>A inspecionar os fluxos de suporte de dados
Muitas vezes tem pretendido saber o fluxo de aspeto assim o desejar que flui através do fluxo de trabalho. Para inspecionar os uma transmissão em fluxo em qualquer momento no fluxo de trabalho, basta clicar uma saída ou entrada pin em qualquer um dos componentes. Neste caso, tente clicar no pin de saída descomprimidos vídeo da nossa entrada de ficheiro do suporte de dados. Uma caixa de diálogo abre-se que permite ao inspecionar o vídeo de saída.

![A inspecionar o pin de saída de vídeo não comprimida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*A inspecionar o pin de saída de vídeo não comprimida*

No nosso caso, indica-nos por exemplo, está a lidar com uma entrada de 1920 x 1080 em 24 fotogramas por segundo em 4: amostragem de 2:2 para um vídeo de quase 2 minutos.

### <a id="MXF_to_MP4_file_generation"></a>Um codificador vídeo para a adicionar. Geração de ficheiros MP4
Tenha em atenção que agora, um vídeo descomprimidos e vários pins de saída de áudio descomprimidos estão disponíveis para utilização no nosso entrada de ficheiro do suporte de dados. Para codificar o vídeo de entrada, é necessário um componente de codificação - neste caso, para a geração. Ficheiros MP4.

Codificar o fluxo de vídeo para 264, adicione o componente de codificador vídeo AVC para a superfície do designer. Este componente assume um fluxo de vídeo uncompress como entrada e disponibiliza um AVC de fluxo de vídeo comprimido no respetivo pin de saída.

![Codificador de AVC desligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codificador de AVC desligado*

As respetivas propriedades determinam como a codificação exatamente ocorre. Vamos tem uma vista de olhos algumas das definições de mais importantes:

* Largura de saída e a altura de saída: estes determinam a resolução do vídeo codificado. No nosso caso, vamos com 640 x 360
* Velocidade de fotogramas: Quando definido como pass-through irá apenas adotar a taxa de moldura de origem, é possível substituir esta apesar. Tenha em atenção que essas conversão framerate não é movimento-compensada.
* Perfil e nível: estes determinam o perfil de AVC e nível. Para comodamente obter mais informações sobre os diferentes níveis e perfis, clique no ícone de ponto de interrogação no componente AVC vídeo codificador e a página de ajuda irão mostrar mais detalhes sobre cada um dos níveis. Para o nosso exemplo, vamos com perfil principal ao nível 3.2 (predefinição).
* Taxa de modo de controlo e velocidade de transmissão (kbps): no nosso cenário, optar por uma constante de velocidade de transmissão (CBR) de saída em 1200 kbps
* Formato de vídeo: Este é sobre VUI (informações de utilização de vídeo) que obtém escrito no fluxo 264 (lado informações sobre o que podem ser utilizados por um descodificador para melhorar a apresentação, mas não essenciais corretamente descodificar):
* NTSC (típico para EUA ou Japão, utilizando 30 fps)
* PAL (típico para a Europa, utilizando 25 fps)
* Modo de dimensionamento GOP: irá ser configurado de tamanho fixo GOP para a nossa fins com uma chave de intervalo de 2 segundos com GOPs fechado. Isto garante que fornece compatibilidade com os serviços de suporte de dados do empacotamento dinâmico do Azure.

Para feed nosso codificador AVC, ligue o pin de saída de vídeo descomprimidos entrada de ficheiro do suporte de dados do componente ao pin entrado do codificador AVC as vídeo descomprimidos.

![Codificador de AVC ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Codificador de AVC principal ligado*

### <a id="MXF_to_MP4_audio"></a>Codificação de sequência de áudio
Neste momento, estamos ter codificado vídeo, mas a sequência de áudio descomprimida original ainda tem de ser comprimido. Para este podemos irá aceda AAC codificação pelo componente AAC codificador (Dolby). Adicione-o para o fluxo de trabalho.

![Codificador de AVC desligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificador AAC desligado*

Agora é não existe uma incompatibilidade: há apenas um único descomprimido áudio entrado pin do codificador AAC enquanto mais provável é que a entrada de ficheiro do suporte de dados terá dois diferentes descomprimidos sequência de áudio disponíveis: um para o canal de áudio esquerdo e outro para a direita. (Se estiver a lidar com surround som, que é 6 canais). Por isso, não é possível ligar diretamente áudio a partir da origem de entrada de ficheiro do suporte de dados para o codificador de áudio AAC. O componente AAC espera uma sequência de áudio "intercalada" so-called: um fluxo único que tenha à esquerda e os canais direita interleaved entre si. Depois de sabemos da nossa ficheiros de suporte de dados de origem que controla áudio é em que posição na origem, pode geramos essa sequência de áudio intercalada com as posições de orador corretamente atribuída para a esquerda e direita.

Primeiro uma melhor gerado um fluxo intercalado a partir os canais de áudio de origem necessários. O componente de áudio fluxo Interleaver irá processar esta-nos. Adicione-o para o fluxo de trabalho e ligue as saídas de áudio da entrada de ficheiro do suporte de dados para a mesma.

![Ligado Interleaver de sequência de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Ligado Interleaver de sequência de áudio*

Agora que temos uma sequência de áudio intercalada, ainda não especificamos onde pretende atribuir as posições de orador esquerda ou direita para. Para poder especificar isto, iremos tirar partido Assigner de posição orador.

![Adicionar um Assigner de posição de orador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Adicionar um Assigner de posição de orador*

Configurar o Assigner de posição de orador para utilização com um fluxo de entrada stereo através de um filtro de configuração predefinida de codificador de "Personalizada" e o canal da configuração predefinida denominada "2.0 (L, R)". (Isto irá atribuir a posição de orador esquerdo para canal 1 e a posição direita orador ao canal de 2.)

Ligar a saída do Assigner de posição orador para a entrada do codificador AAC. Em seguida, informar o codificador de AAC para trabalhar com um "2.0 (L, R)" canal da configuração predefinida, para que confie em lidar com stereo áudio como entrada.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexação fluxos de áudio e vídeo para um contentor de MP4
Fornecido nosso AVC codificado de transmissão e a nossa AAC codificado sequência de áudio, iremos pode capturar ambos para uma. Contentor de MP4. O processo de combinar vários fluxos para um único é chamado "multiplexação" (ou "muxing"). Neste caso, iremos estiver a interleaving áudio e os fluxos de vídeos numa única coherent. Pacote MP4. O componente que coordena isto para um. MP4 contentor é denominado Multiplexer o ISO MPEG-4. Adicione uma à superfície do designer e ligue o codificador de vídeo AVC e codificador AAC entradas.

![Ligado MPEG4 Multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Ligado MPEG4 Multiplexer*

### <a id="MXF_to_MP4_writing_mp4"></a>Escrever o ficheiro MP4
Quando escrever um ficheiro de saída, o componente de saída do ficheiro é utilizado. Iremos pode ligar este à saída do Multiplexer ISO MPEG-4, para que o resultado obtém escrito em disco. Para fazê-lo, ligue o pin de saída do contentor (MPEG-4) para o pin de entrada de escrita de ficheiro de saída.

![Ligado a saída do ficheiro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Ligado a saída do ficheiro*

O nome de ficheiro que será utilizado é determinado pela propriedade de ficheiro. Enquanto que a propriedade pode ser codificado para um determinado valor, provavelmente um pretende configurá-lo através de uma expressão.

Para que o fluxo de trabalho determinar automaticamente o resultado da propriedade de nome de uma expressão de ficheiros, clique em buton junto ao nome do ficheiro (junto ao ícone de pasta). No menu pendente, em seguida, selecione "Expressão". Será apresentado o editor de expressão. Limpe o conteúdo do editor primeiro.

![Editor de expressão vazia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de expressão vazia*

O editor de expressão permite introduzir qualquer valor literal, misturada com uma ou mais variáveis. As variáveis de começar com uma cifrão. Como que prima a tecla de $, o editor de irá mostrar uma caixa de lista pendente com uma opção de variáveis disponíveis. No nosso caso utilizaremos uma combinação da variável de diretório de saída e a variável de nome de ficheiro de entrada base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Preenchido fora do Editor de expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Preenchido fora do Editor de expressão*

> [!NOTE]
> Para ver ver um ficheiro de saída da tarefa codificação no Azure, tem de fornecer um valor no editor de expressão.
>
>

Quando o utilizador confirma a expressão por atingir ok, a janela de propriedade pré-visualize para o valor de propriedade de ficheiro seja resolvido neste ponto no tempo.

![Expressão de ficheiro resolve dir de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Expressão de ficheiro resolve dir de saída*

### <a id="MXF_to_MP4_asset_from_output"></a>Criar um recurso de serviços de suporte de dados a partir do ficheiro de saída
Enquanto é escrever um ficheiro de saída MP4, ainda temos indicar que este ficheiro pertence ao elemento de saída que os media services irão gerar como resultado de executar este fluxo de trabalho. Para este fim, o nó de elemento/ficheiro de saída na tela de fluxo de trabalho é utilizado. Todos os ficheiros de entrada para este nó fará parte do elemento de Media Services do Azure resultante.

Ligar o componente de saída do ficheiro para o componente ficheiros/elemento de saída para concluir o fluxo de trabalho.

![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fluxo de trabalho concluído*

### <a id="MXF_to_MP4_test"></a>Testar o fluxo de trabalho concluído localmente
Para testar localmente o fluxo de trabalho, prima o botão Reproduzir na barra de ferramentas na parte superior. Quando o fluxo de trabalho concluído executar, Inspecione o resultado gerado na pasta de saída configurado. Irá ver o ficheiro de saída MP4 terminar codificado do ficheiro de origem de entrada MXF.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Codificação MXF em MP4 - multibitrate empacotamento dinâmico ativado
Esta explicação passo a passo vamos criar um conjunto de vários ficheiros MP4 de velocidade de transmissão com AAC codificado áudio de um único. Ficheiro de entrada MXF.

Quando uma saída de elemento de transmissão múltipla for pretendida para utilização em combinação com as funcionalidades de empacotamento dinâmico oferecidas pelos serviços de suporte de dados do Azure, vários ficheiros MP4 alinhada GOP de cada uma velocidade de transmissão diferentes e a resolução tem de ser gerado. Para tal, o [MXF de codificação de mensagens em fila para uma MP4 de velocidade de transmissão única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) explicação passo a passo fornece-nos com um ponto de partida.

![Iniciar o fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Iniciar o fluxo de trabalho*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Adicionar um ou mais saídas MP4 adicionais
Todos os ficheiros MP4 no nosso resultante recurso de Media Services do Azure irão suportar uma resolução e velocidade de transmissão diferentes. Vamos adicionar um ou mais ficheiros de saída MP4 ao fluxo de trabalho.

Para se certificar de que poderemos ter todos os nossos codificadores vídeos criadas com as mesmas definições, é mais conveniente duplicar o codificador de vídeo AVC já existente e configurar outra combinação de resolução e velocidade de transmissão (vamos adicionar um 960 x 540 em 25 fotogramas por segundo, Mbps 2,5). Para duplicar o codificador existente, o cópia cole-na superfície do designer.

Ligar o pin de saída de vídeo não comprimida da entrada de ficheiro do suporte de dados para o nosso novo componente AVC.

![Segundo codificador de AVC ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Segundo codificador de AVC ligado*

Agora adapte a configuração para o nosso novo codificador de AVC para 960 x 540 Mbps 2,5 de saída. (Utilizar o respetivo propriedades "largura de saída", "Altura de saída" e "Velocidade de transmissão (kbps)" para este.)

Dado pretende utilizar o elemento resultante, juntamente com o empacotamento dinâmico dos serviços de suporte de dados do Azure, o ponto final de transmissão em fluxo tem de ser capaz de gerar a partir destes ficheiros MP4 fragmentos de HLS/fragmentado MP4/DASH exatamente são alinhados com entre si de uma forma que os clientes que são alternar entre diferentes de forma obtêm um único vídeo contínuo uniforme e experiência áudio. Para se certificar de que surgir, precisamos de Certifique-se de que, nas propriedades de ambos os codificadores AVC o GOP ("grupo de imagens") o tamanho para ambos os ficheiros MP4 está definido como 2 segundos, que pode ser efetuados por:

* definir o modo de tamanho de GOP ao tamanho fixo GOP e
* o intervalo de moldura de chave para dois segundos.
* também definir colocado de são o controlo IDR GOP fechado GOP para garantir que todos os GOP no seus próprio sem dependências

Para tornar a nossa fluxo de trabalho conveniente compreender, mudar o nome do primeiro codificador AVC para "codificador vídeo AVC 640 x 360 1200kbps" e o segundo codificador de AVC "codificador vídeo AVC 960 x 540 2500 kbps".

Agora pode adicione uma segunda ISO MPEG-4 Multiplexer e uma segunda saída de ficheiro. Ligue o multiplexer ao codificador AVC novo e certifique-se de que o resultado é direcionado para o ficheiro de saída. Em seguida, ligar também entrada AAC codificador áudio saída para o novo do multiplexer. A saída do ficheiro por sua vez podem, em seguida, ser ligada para o nó de elemento/ficheiro de saída para adicioná-lo para o elemento de serviços de suporte de dados que será criado.

![Segundo Muxer e ficheiro de saída ligados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Segundo Muxer e ficheiro de saída ligados*

Para compatibilidade com o empacotamento dinâmico de Media Services do Azure, configure de segmento de modo o multiplexer a contagem de GOP ou a duração e defina os GOPs por segmento para 1. (Deve ser a predefinição).

![Modos de segmento de Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos de segmento de Muxer*

Nota: pode pretende repetir este processo para quaisquer outros resolução e velocidade de transmissão combinações que pretende ter adicionado à saída de recurso.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurar os nomes de saída do ficheiro
Temos de mais do que um único ficheiro adicionado ao elemento de saída. Isto fornece uma necessidade para certificar-se de que os nomes dos ficheiros para cada um dos ficheiros de saída são diferentes entre si e talvez mesmo aplicam-se uma convenção de nomenclatura de ficheiros, de modo, torna-se limpar do nome do ficheiro está a lidar com.

Atribuição de nome de saída do ficheiro pode ser controlada através da expressões no designer. Abra o painel de propriedade de um dos componentes de saída do ficheiro e abra o editor de expressão para a propriedade de ficheiro. Nosso primeiro ficheiro de saída foi configurado através da expressão seguinte (consulte o tutorial para que vai da [MXF para uma saída de MP4 de velocidade de transmissão única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Isto significa que o nosso filename é determinado pelo duas variáveis: o directório de saída ao escrever no e o nome de base de ficheiro de origem. A primeira está exposta como uma propriedade na raiz de fluxo de trabalho e a última opção é determinado pelo ficheiro de entrada. Tenha em atenção que o diretório de saída é o que utiliza para fins de teste local; Esta propriedade será substituída pelo motor de fluxo de trabalho quando o fluxo de trabalho é executado pelo processador de suporte de dados baseado na nuvem em Media Services do Azure.
Para dar ambos os nossos ficheiros de saída de uma atribuição de nome de saída consistente, altere o primeiro ficheiro nomenclatura expressão a:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e a segunda para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Execute um teste intermédio executar para se certificar de que ambos os ficheiros de saída MP4 corretamente são gerados.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Adicionar um registo de áudio separada
Como Iremos ver mais tarde quando iremos gerar um ficheiro. ISM em com ficheiros MP4 de saída, iremos irá requerer também um ficheiro MP4 de só de áudio como controlar o áudio para nosso transmissão em fluxo adaptável. Para criar este ficheiro, adicione um muxer adicional para o fluxo de trabalho (ISO-MPEG-4 Multiplexer) e ligar o pin de saída do codificador AAC com o respetivo pin de entrada para controlar 1.

![Áudio Muxer adicionado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Áudio Muxer adicionado*

Crie um terceiro componente de saída do ficheiro para o fluxo de saída do muxer de saída e configurar o ficheiro de nomenclatura expressão como:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Áudio Muxer criação da saída de ficheiro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Áudio Muxer criação da saída de ficheiro*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Adicionar o. ISM SMIL ficheiro
Para o empacotamento dinâmico trabalhar em combinação com MP4 (tanto ficheiros MP4 de só de áudio) no nosso recurso dos Media Services, é também necessário um ficheiro de manifesto (também designado por um ficheiro de "SMIL": sincronizados idioma de integração de suporte). Este ficheiro indica aos Media Services do Azure que ficheiros MP4 estão disponíveis para um empacotamento dinâmico e que um da considerar para a transmissão de áudio. Um ficheiro de manifesto típico para um conjunto do MP4 com uma única sequência de áudio tem o seguinte aspeto:

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

O ficheiro. ISM contém dentro de uma instrução de comutador, uma referência para cada um dos ficheiros de vídeos MP4 individuais e além essas referências de ficheiro de áudio também um (ou mais) para um MP4 que contém apenas áudio.

Gerar o ficheiro de manifesto para o nosso conjunto do MP4 pode ser feito através de um componente chamado "AMS manifesto do escritor de". Para utilizá-lo, arraste-o para a superfície e ligar os pins de saída "Escrever concluída" dos três componentes do ficheiro de saída para a entrada de escritor de manifesto de AMS. Em seguida, certifique-se liguem a saída do escritor de manifesto do AMS para o ficheiro/elemento saída.

Tal como acontece com a nossa outros ficheiros saída componentes, configure o nome de saída do ficheiro de ISM com uma expressão:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Aspeto do nosso fluxo de trabalho concluído o abaixo:

![MXF concluído para o fluxo de trabalho do multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF concluído para o fluxo de trabalho do multibitrate MP4*

## <a id="MXF_to__multibitrate_MP4"></a>Codificação MXF para multibitrate MP4 - blueprint avançada
No [instruções anteriores do fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) viu como um único recurso de entrada MXF pode ser convertido para um elemento de saída com ficheiros MP4 de velocidade de transmissão múltipla, um ficheiro MP4 de só de áudio e um ficheiro de manifesto para utilização em conjunto com o empacotamento dinâmico de Media Services do Azure.

Estas instruções irão mostrar como alguns dos aspetos podem ser melhorados e efetuadas mais conveniente.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Descrição geral do fluxo de trabalho para melhorar a
![Fluxo de trabalho Multibitrate MP4 para melhorar a](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Fluxo de trabalho Multibitrate MP4 para melhorar a*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>As convenções de nomenclatura de ficheiros
O fluxo de trabalho anterior especificamos numa expressão simples como base para gerar nomes de ficheiro de saída. Temos algumas duplicação apesar: todas as os componentes do ficheiro de saída individual especificado essa expressão.

Por exemplo, os nosso componente de saída do ficheiro para o primeiro ficheiro de vídeo está configurado com esta expressão:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Enquanto do resultado da segundo vídeo, temos de uma expressão como:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Não possível limpeza, menos erro suscetível e mais conveniente se podemos foi remova alguns deste duplicação e certifique-coisas mais configuráveis em vez disso? Luckily podemos: capacidades de expressão o estruturador em combinação com a capacidade de criar propriedades personalizadas no nosso raiz de fluxo de trabalho irão dar-numa camada adicional de conveniência.

Vamos assumir que irá unidade filename configuração de forma dos ficheiros MP4 individuais. Estes forma que irá pretendemos configurar num local central (na raiz da nossa gráfico), de onde irá ser acedidos para configurar e a geração de nome de ficheiro de unidade. Para fazer isto, vamos começar por publicar a propriedade de velocidade de transmissão a partir de ambos os codificadores AVC à raiz do nosso fluxo de trabalho, para que fique acessível a partir do tanto a raiz, bem como os codificadores AVC. (Mesmo se apresentados em dois oportunidades diferentes, é apenas um valor subjacente.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Publicação de propriedades do componente para a raiz de fluxo de trabalho
Abra o codificador AVC primeiro, vá para a propriedade de velocidade de transmissão (kbps) e na lista pendente, escolha publicar.

![A propriedade de velocidade de transmissão de publicação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*A propriedade de velocidade de transmissão de publicação*

Configure a caixa de diálogo Publicar a publicar para a raiz do nosso gráfico de fluxo de trabalho, com um nome de "video1bitrate" publicado e um nome a apresentar legível "Velocidade de transmissão do vídeo 1". Configurar um personalizado o nome do grupo chamado "Transmissão em fluxo de forma" e prima publicar.

![A propriedade de velocidade de transmissão de publicação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Caixa de diálogo de publicação para a propriedade de velocidade de transmissão*

Repetir a mesma para a propriedade de velocidade de transmissão do codificador AVC segundo e dê-lhe o nome "video2bitrate" com um nome a apresentar "Vídeo de 2 velocidade de transmissão", no mesmo grupo personalizado "Transmissão em fluxo de forma".

Se agora, iremos inspecionar as propriedades de raiz do fluxo de trabalho, iremos ver nosso grupo personalizado com as duas propriedades publicados apareçam. Ambos são ao refletir o valor do respetivo velocidade de transmissão de codificador de AVC respetivo.

![Propriedades de velocidade de transmissão publicada numa raiz de fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Sempre que queremos de aceder a estas propriedades, a partir do código ou de uma expressão, podemos fazer, como esta:

* a partir do código inline de um componente imediatamente abaixo da raiz: node.getPropertyAsString('../video1bitrate', nulo)
* dentro de uma expressão: ${ROOT_video1bitrate}

Vamos concluir o grupo "Transmissão em fluxo de forma" através da nossa transmissão de áudio controlar no mesmo, bem como a publicação. Dentro as propriedades do codificador AAC, pesquise a definição de velocidade de transmissão e selecione publicar na lista pendente junto ao mesmo. Publicar na raiz do gráfico com o nome "audio1bitrate" e o nome "Velocidade de transmissão do áudio 1" no nosso grupo personalizado "Transmissão em fluxo de forma" a apresentar.

![Caixa de diálogo publicação de velocidade de transmissão de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Caixa de diálogo publicação de velocidade de transmissão de áudio*

![Propriedades de áudio e vídeos resultantes numa raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Propriedades de áudio e vídeos resultantes numa raiz*

Tenha em atenção que a alteração de qualquer um desses três valores também configura novamente e altera os valores nos respetivos componentes que estão ligados com (e onde publicados a partir da).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Ter gerado o ficheiro de saída nomes baseiam-se nos valores de propriedade publicada
Em vez de codificar nosso nomes de ficheiro gerado, iremos pode agora alterar nosso expressão de nome de ficheiro em cada um dos componentes do ficheiro de saída para confiar nas propriedades de velocidade de transmissão que são publicados apenas na raiz do gráfico. Começando com o nosso primeiro saída de ficheiro, localizar a propriedade de ficheiro e editar a expressão como esta:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Os parâmetros diferentes nesta expressão podem ser acedidos e introduzidos por atingir o cifrão no teclado enquanto na janela de expressão. Um dos parâmetros disponíveis é a nossa propriedade video1bitrate que iremos publicados anteriormente.

![Aceder a parâmetros dentro de uma expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Aceder a parâmetros dentro de uma expressão*

Faça o mesmo para a saída do ficheiro para o nosso vídeo segundo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e para a saída do ficheiro só de áudio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se agora, vamos alterar a velocidade de transmissão de qualquer um dos ficheiros de vídeos ou áudio, será reconfigurado de codificador respetivo e a Convenção de nome de ficheiro com base na velocidade de transmissão será cumprida todos os automática.

## <a id="thumbnails_to__multibitrate_MP4"></a>A adição de miniaturas para multibitrate MP4 saída
A partir de um fluxo de trabalho que gera [um multibitrate MP4 um resultado de uma entrada de MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), iremos irá agora ser à procura para a adição de miniaturas para a saída.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Descrição geral do fluxo de trabalho para adicionar as miniaturas para
![Multibitrate MP4 fluxo de trabalho para iniciar a partir de](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4 fluxo de trabalho para iniciar a partir de*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Adicionar JPG codificação
Centro da nossa em miniatura geração será o componente de codificador de JPG, capaz de ficheiros JPG de saída.

![Codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codificador JPG*

Diretamente no entanto, não é possível ligar fluxo nossos vídeo descomprimidos da entrada de ficheiro do suporte de dados para o codificador JPG. Em vez disso, espera possível entregar frames individuais. Esta ação, podemos fazer através de componente de porta de moldura de vídeo.

![Ligar uma porta de moldura ao codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ligar uma porta de moldura ao codificador JPG*

A porta de moldura, uma vez a cada tantas segundos ou frames permite um período de vídeo passar. O intervalo e a hora de deslocamento com que esta situação acontece pode ser configurada nas propriedades.

![Propriedades de porta de moldura vídeos](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriedades de porta de moldura vídeos*

Criemos uma miniatura cada minuto ao definir o modo de tempo (segundos) e o intervalo e 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Lidar com a conversão de espaço de cor
Enquanto seria parecer lógica que agora podem ser ligados pins ambas as vídeo descomprimidos da porta de moldura e a entrada de ficheiro do suporte de dados, iremos irá obter um aviso se seria fazemo-lo.

![Erro de espaço de cor de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erro de espaço de cor de entrada*

Isto acontece porque o modo em que colour informações são representadas na nossa original em bruto descomprimido vídeo sequência provenientes da nossa MXF é diferente do que o codificador de JPG está à espera. Mais especificamente, uma so-called "cor espaço" de "RGB" ou "Em tons de cinzento" é esperado fluem. Isto significa que entrada transmissão a porta moldura vídeo tem de ter uma conversão aplicada sobre o espaço de cor de primeiro.

Arraste para o fluxo de trabalho o conversor de espaço de cor - Intel e ligue-o ao nosso porta da moldura.

![Ligar um Convertor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Ligar um Convertor de espaço de cor*

Na janela de propriedades, escolha a entrada de BGR 24 na lista de predefinição.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Escrever miniaturas
Diferente do vídeo da nossa MP4, o componente de codificador JPG será saída mais de um ficheiro. Para lidar com isto, um componente de pesquisa de cenas JPG escritor de ficheiro pode ser utilizado: vai demorar entradas miniaturas JPG e escrevê-las, cada nome de ficheiro que está a ser o sufixo por um número diferente. (O número, normalmente, que indica o número de segundos/unidades na sequência que a miniatura foi desenhada a partir de.)

![Introduzir o escritor de ficheiro JPG de pesquisa de cenas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introduzir o escritor de ficheiro JPG de pesquisa de cenas*

Configurar a propriedade do caminho da pasta de saída com a expressão: ${ROOT_outputWriteDirectory}

e a propriedade de prefixo de nome de ficheiro com:

    ${ROOT_sourceFileBaseName}_thumb_

O prefixo determinará a forma como os ficheiros em miniatura estão a ser com o nome. Estes irão ser com o sufixo um número que indica a posição do botão na sequência.

![Propriedades de pesquisa JPG ficheiro escritor cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriedades de pesquisa JPG ficheiro escritor cena*

Ligar o escritor de ficheiro do cena pesquisa JPG ao nó de elemento/ficheiro de saída.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Detetar erros num fluxo de trabalho
Ligue a entrada do conversor de espaço de cor à saída de vídeo descomprimida não processada. Agora a efetuar uma execução do fluxo de trabalho de teste local. Há a possibilidade de boa o fluxo de trabalho, irá parar de executar e indicar com um contorno vermelho no componente que encontrou um erro:

![Erro de conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erro de conversor de espaço de cor*

Clique no ícone "E" red pouca no canto superior direito canto do componente de cor espaço conversor para ver o que é o motivo pelo qual a tentativa de codificação falhou.

![Caixa de diálogo de erro de conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Caixa de diálogo de erro de conversor de espaço de cor*

Transforma, como pode ver, que o espaço de cor entrada padrão para o conversor de espaço de cor tem de ser rec601 para a nossa conversão pedida de YUV para RGB. Parecer nosso fluxo não indica de rec601. (Rec 601 é um padrão para codificação entrelaçadas analógica sinais de vídeos em formato digital de vídeo. Especifica uma região de Active Directory que abrangem 720 amostras de luminance e 360 chrominance amostras por linha. A cor do sistema de codificação é conhecida como YCbCr 4:2:2.)

Para corrigir isto, iremos irá indicar nos metadados do nosso fluxo que estamos a lidar com rec601 conteúdo. Para fazê-lo iremos utilizar um componente do Atualizador de tipo de dados de vídeo, vamos pôr em prática entre nosso origem em bruto e o componente de conversão de espaço de cor. Este atualizador do tipo de dados permite que a atualização manual das certos dados vídeos propriedades do tipo. Configure para indicar um padrão de espaço de cor de "Rec 601". Isto fará com que o Atualizador de tipo de dados de vídeo marcar o fluxo com o espaço de cor "Rec 601" se tiver ocorrido não existe espaço de cor definido ainda. (Esta não substituirão quaisquer metadados existente, a menos que a caixa de verificação de substituição tiver sido selecionada.)

![Atualizar o padrão de espaço de cor no Atualizador de tipo de dados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Atualizar o padrão de espaço de cor no Atualizador de tipo de dados*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Fluxo de trabalho concluído
Agora que a nossa nosso fluxo de trabalho estiver concluído, efetue outro teste executar para vê-lo a transmitir.

![Fluxo de trabalho concluído para o resultado de várias mp4 com miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fluxo de trabalho concluído para o resultado de várias mp4 com miniaturas*

## <a id="time_based_trim"></a>Corte de baseados no tempo de mensagens em fila de saída multibitrate MP4
A partir de um fluxo de trabalho que gera [um multibitrate MP4 um resultado de uma entrada de MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), iremos irá agora ser à procura no trimming o vídeo de origem com base no carimbos de data / hora.

### <a id="time_based_trim_start"></a>Descrição geral do fluxo de trabalho para começar a adicionar corte para
![A partir do fluxo de trabalho adicionar corte para](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*A partir do fluxo de trabalho adicionar corte para*

### <a id="time_based_trim_use_stream_trimmer"></a>Utilizar o Trimmer de fluxo
Permite que o componente de fluxo Trimmer cortar início e fim de um fluxo de entrada base em temporização informações (segundos, minutos,...). O trimmer não suporta corte baseada no intervalo.

![Fluxo Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Fluxo Trimmer*

Em vez de ligar o codificadores AVC e assigner de posição de orador para a entrada de ficheiro do suporte de dados diretamente, vamos pôr em prática entre os trimmer o fluxo. (Um para o sinal de vídeo e outro para o sinal de áudio intercalado.)

![Colocar em entre Trimmer de fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Colocar em entre Trimmer de fluxo*

Vamos configurar o trimmer para que iremos só irá processar vídeos e áudio entre 15 segundos e 60 segundos as vídeo.

Vá para as propriedades de Trimmer de fluxo de vídeo e configurar a hora de início (15s) e propriedades de hora de fim (60s). Para certificar-se de que os nosso trimmer de áudio e vídeo sempre está configurado para o mesmo valores inicial e final, vamos publicar os à raiz do fluxo de trabalho.

![Publicar a propriedade de hora de início do fluxo Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicar a propriedade de hora de início do fluxo Trimmer*

![Publicar a caixa de diálogo de propriedades para a hora de início](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicar a caixa de diálogo de propriedades para a hora de início*

![Publicar a caixa de diálogo de propriedades para a hora de fim](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publicar a caixa de diálogo de propriedades para a hora de fim*

Se podemos agora inspecionar a raiz do nosso fluxo de trabalho, ambas as propriedades serão neatly apresentados e configuráveis a partir daí.

![Propriedades publicadas disponíveis em raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Propriedades publicadas disponíveis em raiz*

Agora abra as propriedades de corte a partir de áudio trimmer e configurar as horas de início e de fim com uma expressão que se refere às propriedades de publicados na raiz da nossa fluxo de trabalho.

Para a hora de início de áudio corte:

    ${ROOT_TrimmingStartTime}

e para a respetiva hora de fim:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Fluxo de trabalho concluído
![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fluxo de trabalho concluído*

## <a id="scripting"></a>Introduzir o componente de script
Componentes com script podem executar scripts arbitrários durante as fases de execução do nosso fluxo de trabalho. Existem quatro scripts diferentes que podem ser executadas, cada um com características específicas e os seus próprios local no ciclo de vida do fluxo de trabalho:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

A documentação do componente convertidos em script fica mais detalhadamente para cada um dos acima. No [a secção seguinte](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), a **realizeScript** componente script é utilizado para construir um xml cliplist no momento quando o fluxo de trabalho é iniciado. Este script chama-se durante a configuração do componente, o que acontece apenas uma vez no ciclo de vida de.

### <a id="scripting_hello_world"></a>Script num fluxo de trabalho: Olá, mundo
Arraste um componente de criar um script para a superfície do designer e mude o nome (por exemplo, "SetClipListXML").

![Adicionar um componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente com script*

Quando inspecionar as propriedades do componente convertidos em script, os quatro tipos de script diferente será mostrado, cada configuráveis para um script diferente.

![Propriedades do componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente de script*

Limpe o processInputScript e abra o editor de realizeScript. Agora vamos está tudo configurado e pronto para iniciar scripting.

Scripts são escritos no Groovy, uma linguagem de script compilada dinamicamente para a plataforma de Java que mantém a compatibilidade com o Java. Na realidade, o código de Java a maioria das é código Groovy válido.

Vamos escrever um script de groovy mundo Olá simples no contexto do nosso realizeScript. Introduza o seguinte no editor:

    node.log("hello world");

Agora a executar uma execução de teste local. Após a execução, Inspecione a propriedade de registos (através do separador de sistema no componente convertidos em script).

![Saída de registo do Olá mundo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Saída de registo do Olá mundo*

O objeto de nó, podemos chamar o método de registo, refere-se à nossa "nó" atual ou o componente que está a scripting dentro. Todos os componentes como tal, tem a capacidade de dados de registo de saída, disponível através do separador de sistema. Neste caso, iremos saída o literal de cadeia "Olá mundo". É importante compreender aqui é que este provar pode ser uma ferramenta de depuração valiosas, fornecendo-lhe conhecimentos aprofundados sobre de que o script está, na verdade, a fazer.

A partir no nosso ambiente de script, iremos também tem acesso às propriedades nos outros componentes. Experimente este:

    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

A nossa janela de registo irá mostrar-no seguinte:

![Saída de registo para aceder aos caminhos de nó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Saída de registo para aceder aos caminhos de nó*

## <a id="frame_based_trim"></a>Com base no período de corte de saída multibitrate MP4
A partir de um fluxo de trabalho que gera [um multibitrate MP4 um resultado de uma entrada de MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), iremos irá agora ser à procura no trimming o vídeo de origem com base nas contagens de moldura.

### <a id="frame_based_trim_start"></a>Descrição geral de blueprint para começar a adicionar corte para
![Fluxo de trabalho para começar a adicionar corte para](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Fluxo de trabalho para começar a adicionar corte para*

### <a id="frame_based_trim_clip_list"></a>Utilizando a lista de Clip XML
Todos os tutoriais de fluxo de trabalho anterior, utilizámos o componente de entrada de ficheiro do suporte de dados como nossa vídeo origem de entrada. Para este cenário específico, iremos utilizar o componente de origem da lista de Clip em vez disso. Tenha em atenção que esta não deve ser a preferencial forma de trabalho; Utilize apenas a origem da lista de Clip quando existe um motivo real para o fazer (tal como no abaixo caso, onde que estamos a efetuar a utilização das capacidades de corte de lista de clip).

Para mudar do nosso entrada de ficheiro do suporte de dados para a origem da lista de Clip, arraste o componente de origem da lista de Clip para a superfície de desenho e ligar o pin Clip lista XML para o nó XML de lista Clip do estruturador de fluxo de trabalho. Isto deve preencher a origem da lista de Clip com pins de saída, em conformidade com os nosso vídeo de entrada. Agora, ligar os pins as vídeo não comprimidos e descomprimidos áudio da a origem da lista de Clip aos respetivos AVC codificadores e Interleaver de sequência de áudio. Agora, remova a entrada de ficheiro do suporte de dados.

![Substituir o ficheiro de entrada de suporte de dados com a origem da lista de Clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Substituir o ficheiro de entrada de suporte de dados com a origem da lista de Clip*

O componente de origem da lista de Clip aceita como entrada um "XML de lista de Clip". Quando selecionar o ficheiro de origem para testar com localmente, este xml de lista clip é preenchido automaticamente para si.

![Propriedade de Clip lista XML auto-preenchida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriedade de Clip lista XML auto-preenchida*

Procura um pouco mais próximo do xml, este é o aspeto que tem:

![Caixa de diálogo de lista de clip editar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Caixa de diálogo de lista de clip editar*

Isto no entanto não reflete as capacidades do xml de lista clip. Temos uma das alternativas consiste em Adicionar um elemento de "Compactação" em ambas as vídeo, áudio e origem, como esta:

![Adicionar um elemento de compactação à lista de clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Adicionar um elemento de compactação à lista de clip*

Se modificar o xml de lista clip como esta acima e efetuar um teste local executar, irá ver o vídeo foi corretamente cortada entre 10 e 20 segundos as vídeo.

Contrária ao disposto o que acontece quando fizer uma execução local entanto, este xml cliplist muito mesmo não teriam o mesmo efeito quando aplicada num fluxo de trabalho que é executado em Media Services do Azure. Quando é iniciado o codificador de Premium do Azure, o xml de cliplist é gerado sempre novamente, com base no ficheiro de entrada, a que tarefa de codificação foi introduzida. Isto significa que quaisquer alterações que fazemos no xml Infelizmente deverá ser substituídas.

Para o contador cliplist xml que está a ser eliminado quando é iniciada uma tarefa de codificação, iremos pode voltar gerá-la no momento imediatamente após o início do nosso fluxo de trabalho. Essas ações personalizadas podem ser criadas através do qual é denominado um "Component convertidos em script". Para obter mais informações, consulte [introduzindo o componente convertidos em script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Arraste um componente de criar um script para a superfície do designer e renomeie-o para "SetClipListXML".

![Adicionar um componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente com script*

Quando inspecionar as propriedades do componente convertidos em script, os quatro tipos de script diferente será mostrado, cada configuráveis para um script diferente.

![Propriedades do componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente de script*

### <a id="frame_based_trim_modify_clip_list"></a>Modificar a lista de clip de um componente convertidos em script
Antes de voltar iremos poder escrever o xml de cliplist que é gerado durante o arranque do fluxo de trabalho, precisamos de ter acesso para a propriedade de xml cliplist e o conteúdo. Pode fazemo-lo como esta:

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Lista de clip entrada que está a ser registada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clip entrada que está a ser registada*

Primeiro é preciso uma forma de determinar a partir do qual ponto até que ponto queremos cortar o vídeo. Para tornar esta prática ao utilizador menos técnica do fluxo de trabalho, publica duas propriedades para a raiz do gráfico. Para tal, clique com o botão direito do rato na superfície do designer e selecione "Adicionar propriedade de":

* Primeira propriedade: "ClippingTimeStart" do tipo: "TIMECODE"
* Segunda propriedade: "ClippingTimeEnd" do tipo: "TIMECODE"

![Adicionar caixa de diálogo de propriedades para a hora de início de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adicionar caixa de diálogo de propriedades para a hora de início de recorte*

![Publicado recorte propriedades de tempo na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicado recorte propriedades de tempo na raiz do fluxo de trabalho*

Configure ambas as propriedades para um valor adequado:

![Configurar o início de recorte e propriedades de fim](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurar o início de recorte e propriedades de fim*

Agora, de dentro do nosso script, iremos podem aceder a ambas as propriedades, como esta:

    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Janela de registo que mostra início e fim de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Janela de registo que mostra início e fim de recorte*

Vamos analisar as cadeias de timecode para um mais conveniente utilizar o formulário, utilizando uma expressão regular simple:

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);

![Janela de registo com o resultado do timecode analisado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Janela de registo com o resultado do timecode analisado*

Com estas informações em execução, agora, pode modificar o xml de cliplist para refletir as horas de início e de fim para o recorte de moldura exata pretendido de filmes.

![Código de script para adicionar elementos compactação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código de script para adicionar elementos compactação*

Isto foi feito através de operações de manipulação de cadeia normal. O xml de lista clip modificado resultante não é gravado para a propriedade clipListXML na raiz de fluxo de trabalho através do método "setProperty". A janela de registo após a execução de outro teste seria mostram-no seguinte:

![Registo lista clip resultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registo lista clip resultante*

Efetue um teste-executar para ver como os fluxos de áudio e vídeos tem sido anexados. Como vai fazer mais do que um teste-execução com diferentes valores para os pontos de corte, irá notar que às serão não ser levados em consideração no entanto! O motivo para isto é que o estruturador, ao contrário do tempo de execução do Azure, não substitui o xml de cliplist cada execução. Isto significa que só muito pela primeira vez que definiu o de entrada e saída pontos, fará com que o xml transformar, todas as outras vezes, nosso cláusula de proteção (se (clipListXML.indexOf ("<trim>") = = -1)) irá impedir que o fluxo de trabalho adicionar outro elemento compactação quando já existe um presente.

Para tornar a nossa fluxo de trabalho prático testar localmente, melhor adicionamos alguns códigos de manutenção próxima inspeciona se um elemento de compactação já estava presente. Se assim for, iremos removê-lo antes de continuar, modificando o xml com os novos valores. Em vez de utilizar manipulações de cadeia simples, é provavelmente mais segura para fazê-lo através do modelo de objeto de real xml análise.

Antes de pode adicionamos desse código, embora, vamos precisar de adicionar primeiro um número de declarações de importação no início do nosso script:

    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

Depois da primeira, adicionamos o código de limpeza necessários:

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

Este código passa apenas acima ponto em que adicionamos os elementos de compactação ao cliplist XML.

Neste momento, estamos poderá executar e modificar nosso fluxo de trabalho como sucederia com queremos enquanto tem alterações que foram aplicadas tempo.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Adicionar uma propriedade de conveniência ClippingEnabled
Como não poderá sempre corte acontecer, vamos concluir desativar nosso fluxo de trabalho, adicionando um conveniente sinalizador booleano que indica se é ou não queremos ativar corte / recorte.

Tal como anteriormente, publicar uma nova propriedade à raiz do nosso fluxo de trabalho chamado "ClippingEnabled" de tipo "BOOLEAN".

![Publicar uma propriedade para ativar recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicar uma propriedade para ativar recorte*

Com o abaixo cláusula guard simples, pode verificar se é necessário o corte e decidir se nossa lista de clip como tal, tem de ser modificado ou não.

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }


### <a id="code"></a>Código de conclusão
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }


## <a name="also-see"></a>Consulte também
[Introdução às Premium codificação, no suporte de dados do Azure, os serviços](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como utilizar o Premium codificação, no suporte de dados do Azure, os serviços](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[A codificação de conteúdo a pedido com o serviço de multimédia do Azure](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formatos e Codecs de Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-premium-workflow-encoder-formats.md)

[Ficheiros de fluxo de trabalho de exemplo](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta de Explorador de serviços de suporte de dados do Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
