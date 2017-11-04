---
title: "Incorporar um vídeo de transmissão em fluxo adaptável MPEG-DASH numa aplicação HTML5 com DASH.js | Microsoft Docs"
description: "Este tópico demonstra como incorporar um vídeo de transmissão em fluxo adaptável MPEG-DASH numa aplicação com DASH.js HTML5."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 27ce6325773ba1f9fd9cd9ab9e07ea9f5e2488ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incorporar um vídeo de transmissão em fluxo adaptável MPEG-DASH numa aplicação HTML5 com DASH.js
## <a name="overview"></a>Descrição geral
MPEG-DASH é uma norma ISO para a transmissão em fluxo adaptável de conteúdos de vídeo, que oferece vantagens significativas para quem pretende fornecer as vídeo de alta qualidade, adaptável transmissão em fluxo de saída. Com MPEG-DASH, o fluxo de vídeo irá remover automaticamente a uma definição inferior quando a rede fique congestionada. Isto reduz a probabilidade do Visualizador de ver um vídeo "em pausa" enquanto o leitor de transfere os seguintes alguns segundos para reproduzir (também conhecido como colocação em memória intermédia). Como reduz o congestionamento de rede, o leitor de vídeo por sua vez irá devolver um fluxo de qualidade superior. Esta capacidade para se adaptar a largura de banda necessária também resulta numa hora de início rápida de vídeo. Isto significa que o primeiro alguns segundos podem ser reproduzidos num segmento qualidade inferior fast para transferência e, em seguida, passo até um conteúdo de uma vez suficientes qualidade superior tem foi colocado na memória intermédia.

Dash.js é um código aberto MPEG-DASH leitor de vídeo escrito em JavaScript. O objetivo consiste em fornecer um leitor robusto, entre plataformas que pode ser livremente reutilizado em aplicações que necessitam de reprodução de vídeo. Fornece reprodução de MPEG-DASH em qualquer browser que suporte hoje o W3C suporte de dados de origem extensões (MSE), que é Chrome, Microsoft Edge e IE11 (noutros browsers indicou respetivo objetivo para suportar MSE). Para obter mais informações sobre DASH.js, js consulte o repositório do GitHub dash.js.

## <a name="creating-a-browser-based-streaming-video-player"></a>Criar um leitor de vídeo transmissão em fluxo com base no browser
Para criar uma página web simples que apresenta um leitor de vídeo com a esperada controla como um play, colocar em pausa, rewind etc., precisa de:

1. Criar uma página HTML
2. Adicione a etiqueta de vídeo
3. Adicionar o leitor de dash.js
4. Inicializar o leitor
5. Adicionar alguns estilo CSS
6. Ver os resultados num browser que implementa MSE

A inicializar o leitor pode ser concluída em apenas alguns a linhas de código JavaScript. Utilizar dash.js, realmente é que simples incorporar as vídeo MPEG-DASH nas suas aplicações baseadas no browser.

## <a name="creating-the-html-page"></a>Criar página HTML
O primeiro passo é criar um padrão HTML página que contém o **vídeo** elemento, guarde este ficheiro como basicPlayer.html, como o exemplo seguinte ilustra:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

## <a name="adding-the-dashjs-player"></a>A adição de leitor de DASH.js
Para adicionar a implementação de referência dash.js à aplicação, terá de captar o ficheiro dash.all.js a 1.0 versão do projeto dash.js. Este deve ser guardado na pasta JavaScript da aplicação. Este ficheiro é um ficheiro de conveniência que obtém em conjunto o todo o código necessário dash.js num único ficheiro. Se tiver um aspeto à volta o repositório de dash.js, irá encontrar os ficheiros individuais, testar código e muito mais, mas se todos os que pretende efetuar está dash.js utilize, em seguida, o ficheiro de dash.all.js é o que precisa.

Para adicionar o leitor de dash.js às suas aplicações, adicione uma etiqueta de script para a secção principal de basicPlayer.html:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Em seguida, crie uma função ao inicializar o leitor quando carrega a página. Adicione o seguinte script após a linha em que carrega dash.all.js:

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Esta função cria um DashContext pela primeira vez. Isto é utilizado para configurar a aplicação para um ambiente específico de tempo de execução. Do ponto de vista técnico, define as classes que o framework de injeção de dependência deve utilizar ao construir a aplicação. Na maioria dos casos, irá utilizar Dash.di.DashContext.

Em seguida, instanciar a classe principal do dash.js framework, MediaPlayer. Esta classe contém as principais métodos necessários, tal como reproduzir e colocar em pausa, gere a relação com o elemento de vídeo e também a interpretação do ficheiro de descrição de apresentação de suporte de dados (MPD) que descreve o vídeo para ser reproduzido.

A função de startup() da classe MediaPlayer denomina-se para se certificar de que o leitor está pronto para reprodução de vídeo. Entre outras coisas esta função assegura que todas as classes necessárias (conforme definido pelo contexto) foram carregadas. Assim que o leitor estiver pronto, pode anexar o elemento de vídeo ao mesmo utilizando a função de attachView(). Isto permite que o MediaPlayer inserir o fluxo de vídeo no elemento e também controlar reprodução conforme necessário.

Transmita o URL do ficheiro MPD para o MediaPlayer para que o se conhece o vídeo que é esperado para reproduzir. A função de setupVideo() acabou de criar terão de ser executadas assim que a página totalmente carregou. Fazê-lo utilizando o evento de onload do elemento de corpo. Alterar o <body> elemento:

    <body onload="setupVideo()">

Por fim, defina o tamanho do elemento de vídeo através de CSS. Num ambiente de transmissão em fluxo adaptável, isto é especialmente importante porque o tamanho de vídeo a ser reproduzido pode alterar como reprodução feita para alterar as condições de rede. Nesta demonstração simple simplesmente forçar o elemento de vídeo para ser 80% da janela do browser disponíveis através da adição do CSS seguintes para a secção principal da página:

    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

## <a name="playing-a-video"></a>Reproduzir um vídeo
Reproduzir um vídeo, aponte o seu browser para o ficheiro basicPlayback.html e clique em play no leitor de vídeo apresentado.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja Também
[Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)

[Repositório do GitHub dash.js](https://github.com/Dash-Industry-Forum/dash.js) 

