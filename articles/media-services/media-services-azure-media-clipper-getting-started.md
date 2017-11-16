---
title: "Introdução ao Recortador de suporte de dados do Azure | Microsoft Docs"
description: "Introdução de Recortador de suporte de dados do Azure, uma ferramenta para criar o suporte de dados de aplicações de clips de recursos"
services: media-services
keywords: "Clip; subclip codificação; suporte de dados"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8a4f2c79131664ca0d078fa58c6a75b54243e705
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Criar aplicações de clips com Recortador de suporte de dados do Azure
Esta secção mostra-lhe os passos básicos de introdução ao Azure Recortador de suporte de dados. Secções que se seguem fornecem as informações específicas sobre como configurar o Recortador de suporte de dados do Azure.

- Primeiro, adicione as seguintes ligações para o leitor de multimédia do Azure e o Recortador de suporte de dados do Azure para o cabeçalho do documento. Recomendamos que especifique explicitamente uma versão do leitor de multimédia do Azure e Recortador nos URL. Não utilize a versão mais recente destes recursos na produção, dado que estão sujeitas a alterações a pedido.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Em seguida, adicione as seguintes classes para o elemento de div onde pretende instanciar o Recortador.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcionalmente, para ativar o tema escuro, adicione a classe de skin escuros:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Em seguida, instanciar o Recortador com a seguinte chamada de API:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Os parâmetros para a chamada de método de inicialização são:
- `selector`{NECESSÁRIO, cadeia}: Seletor CSS do elemento HTML correspondente onde deve ser composto o widget.
- `restVersion`{NECESSÁRIO, cadeia}: versão a API de REST do serviços de suporte de dados do Azure de destino. A versão REST define o formato de saída gerado pelo widget. Atualmente, apenas 2.0 é suportada.
- `submitSubclipCallback`{Promessa necessário} A função de chamada de retorno invocada quando o botão "Submeter" o widget é clicado. A função de chamada de retorno deve esperar que o resultado gerado pelo widget (uma configuração de tarefa de composição ou uma definição de filtro). Para obter mais informações, consulte a chamada de retorno de subclip de envio.
- `logLevel`{OPCIONAL, {'' informações' ', 'Aviso', 'error'}}: O nível de registo que será apresentado na consola do browser. Valor predefinido: erro
- `minimumMarkerGap`{OPCIONAL, int}: O tamanho mínimo de um subclip (em segundos). Nota: o valor deve ser maior ou igual a 6, que também é a predefinição.
- `singleBitrateMp4Profile`{OPCIONAL, um objeto JSON} O perfil de mp4 de velocidade de transmissão única a utilizar para a configuração de tarefa de composição gerada pelo widget. Se não for indicado, utiliza o [predefinido perfil MP4 de velocidade de transmissão única](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile`{OPCIONAL, um objeto JSON} O perfil de mp4 de velocidade de transmissão múltipla a utilizar para compor a configuração de tarefas gerada pelo widget. Se não for indicado, utiliza o [predefinido perfil MP4 de velocidade de transmissão múltipla](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap`{OPCIONAL, um objeto json} Permite personalizar os atalhos de teclado o widget. Para obter mais informações, consulte [atalhos de teclado personalizável](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback`{Promessa opcional} A função de chamada de retorno invocada para carregar (no modo assíncrono) uma nova página de recursos para o painel de recursos sempre que o utilizador desloca para baixo para a parte inferior do painel. Para obter mais informações, consulte a chamada de retorno do recurso painel carregador.
- `height`{OPCIONAL, number} A altura total o widget (altura mínima é 600 px sem painel de recursos e 850 px com o painel de recursos).
- `subclippingMode`(Opcional, {'all', 'compor', "filtro"}): O mode(s) subclipping permitido. O valor predefinido é tudo.
- `filterAssetsTypes`(Opcional, bool): filterAssetsTypes permitem-lhe mostrar/ocultar a lista pendente de filtros do painel de recursos. O valor predefinido é verdadeiro.
- `speedLevels`(Opcional, matriz): speedLevels permite definir níveis diferentes de velocidade para o leitor de vídeo, consulte [documentação do Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) para obter mais informações.
- `resetOnJobDone`(Opcional, bool): resetOnJobDone permite Recortador para repor o subclipper para um Estado inicial quando um trabalho for submetido com êxito.
- `autoplayVideo`(Opcional, bool): autoplayVideo permite Recortador a reprodução automática ROM o vídeo em carga. O valor predefinido é verdadeiro.
- `language`{OPCIONAL, cadeia}: linguagem define o idioma o widget. Se não for especificado, a miniaplicação tenta localizar as mensagens com base no idioma do browser. Não se for detetado nenhum idioma no browser, será assumida a miniaplicação inglês. Para obter mais informações, consulte a secção de idiomas suportados.
- `languages`{OPCIONAL, JSON}: o parâmetro de idiomas substitui o dicionário predefinido de idiomas com um dicionário personalizado definido pelo utilizador. Para obter mais informações, consulte a secção de idiomas suportados.
- `extraLanguages`(Opcional, JSON): o parâmetro extraLanaguages adiciona novos idiomas ao dicionário predefinido. Para obter mais informações, consulte a secção de idiomas suportados.

## <a name="typescript-definition"></a>Definição de typeScript
A [TypeScript](https://www.typescriptlang.org/) é possível encontrar o ficheiro de definição para o Recortador [aqui](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Recortador de Media Services do Azure API
Esta secção documenta a superfície de API fornecida pelo Recortador.

- `load(assets)`: carrega uma lista de recursos para o painel de recursos (não deve ser utilizada em conjunto com `assetsPanelLoaderCallback`). Consulte este [artigo](media-services-azure-media-clipper-load-assets.md) para obter detalhes sobre como carregar recursos para o Recortador.
- `setLogLevel(level)`: define o nível de registo que será apresentado na consola do browser. Os valores possíveis são: `info`, `warn`, `error`.
- `setHeight(height)`: define a altura total o widget em pixels (altura mínima é 600 px sem painel de recursos e 850 px com o painel de recursos).
- `version`: obtém a versão de widget.

## <a name="configuring-azure-media-clipper"></a>Configurar o Recortador de Media Services do Azure
Consulte os passos seguintes para configurar o Recortador de suporte de dados do Azure:
- [Carregar recursos para o Recortador de suporte de dados do Azure](media-services-azure-media-clipper-load-assets.md)
- [Configurar os atalhos de teclado personalizado](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Submeter tarefas de recorte do Recortador](media-services-azure-media-clipper-submit-job.md)

## <a name="supported-languages"></a>Linguagens suportadas
O widget Recortador está disponível em 18 idiomas. Para definir o idioma de widget, tem de definir o `language` parâmetro durante a inicialização. Passagem da cadeia de código de idioma pretendido na lista seguinte:
- Chinês (simplificado): zh-hans
- Chinês (tradicional): zh-hant
- Checo: cs
- Neerlandês, Flemish: nl
- Inglês: en
- Francês: fr
- Alemão: Alemanha
- Húngaro: hu
- Italiano:-
- Japonês: ja
- Coreano: ko
- Polaco: LP
- Português (Brasil): pt-br
- Português (Portugal): pt-pt
- Russo: ru
- Espanhol: es
- Sueco: sv
- Turco: tr

Para definir um dicionário de idioma personalizado ou expandir o dicionário de idioma predefinido, tem de definir o `languages` ou `extraLanguages` parâmetro, respetivamente. Transmita um dicionário personalizado utilizando o seguinte formato JSON:

```javascript
{
      "{language-code}":
          "{message-id}": "{message}"
          ...
      }
      ...
}
```

Por exemplo, o exemplo seguinte define as cadeias localizadas em inglês:

```javascript
export default {
  'VideoPlayer.noPreview': 'No video preview',
  'VideoPlayer.loadAsset': 'You must provide a valid asset',
  'AssetsPanel.name': 'Name',
  'AssetsPanel.type': 'Asset type',
  'AssetsPanel.actions': 'Actions',
  'AssetsPanel.loading': 'Loading...',
  'AssetsPanel.duration': 'Duration',
  'AssetsPanel.resolution': 'Resolution',
  'AssetsPanel.pluralFiles': '{0} assets',
  'AssetsPanel.searchFiles': 'Search assets',
  'AssetsPanel.showTypes': 'Show:',
  'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
  'AssetsPanel.filterTypes': 'Filters',
  'AssetsPanel.assetTypes': 'Assets',
  'AssetsPanel.assetsAll': 'All',
  'AssetsPanel.addAsset': 'Add asset to the end',
  'AssetsPanel.addFilter': 'Add filter to the timeline',
  'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
  'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
  'AssetsPanel.live': 'LIVE',
  'AssetsPanel.unknown': 'UNKNOWN',
  'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
  'VideoPlayer.openAdvancedSettings': 'Advanced settings',
  'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
  'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
  'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
  'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
  'Common.cancel': 'Cancel',
  'Common.OK': 'OK',
  'AdvancedSettings.framerate': 'Frame rate',
  'Dropdown.select': 'Select an option...',
  'InputAsset.RemoveInput': 'Remove source',
  'Zoom.startTime': 'Start time',
  'Zoom.endTime': 'End time',
  'VideoPlayer.subclips': 'Subclips:',
  'VideoPlayer.length': 'Clip length:',
  'Accordion.scrollLeft': 'Scroll to the left',
  'Accordion.scrollRight': 'Scroll to the right',
  'AdvancedSettings.title': 'Advanced settings',
  'AdvancedSettings.subclipName': 'Subclip name',
  'AdvancedSettings.subclipType': 'Subclipping mode',
  'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
  'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
  'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
  'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
  'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
  'AdvancedSettings.singleAudioTrack': '1 audio track selected',
  'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
  'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
  'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
  'AssetsPanel.loadingError': 'Failed to retreive assets from server.',
  'AssetsPanel.retry': 'Retry?',
  'CommandBar.prevFrameTitle': 'Back up one frame',
  'CommandBar.prevKeyFrameTitle': 'Back up one GOP',
  'CommandBar.cleanJob': 'Remove all assets',
  'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
  'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
  'CommandBar.update': 'Update filter',
  'CommandBar.createFilter': 'Create filter',
  'CommandBar.submit': 'Submit subclipper job',
  'CommandBar.jobErrorTitle': 'Operation failed',
  'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
  'CommandBar.markInTitle': 'Set in at playhead',
  'CommandBar.markInPosition': 'Mark in timecode',
  'CommandBar.markOutTitle': 'Set out at playhead',
  'CommandBar.markOutPosition': 'Mark out timecode',
  'CommandBar.nextFrameTitle': 'Advance one frame',
  'CommandBar.nextKeyFrameTitle': 'Advance one GOP',
  'CommandBar.play': 'Play video',
  'CommandBar.pause': 'Pause video',
  'CommandBar.playPreviewTitle': 'Play subclip preview',
  'CommandBar.pausePreviewTitle': 'Pause subclip preview',
  'CommandBar.redoTitle': 'Redo last action',
  'CommandBar.removeAsset': 'Remove current asset',
  'CommandBar.undoTitle': 'Undo last action',
  'VideoPlayer.errorTitle': 'Error',
  'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
  'Timeline.markIn': 'Mark in bracket',
  'Timeline.markOut': 'Mark out bracket',
  'Timeline.playHead': 'Play head',
};
```