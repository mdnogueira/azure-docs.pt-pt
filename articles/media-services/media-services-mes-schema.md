---
title: "Esquema de codificador de multimédia Standard | Microsoft Docs"
description: "O tópico fornece uma descrição geral do esquema codificador de multimédia Standard."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 0d034e2c3827b297173262d294a2e566a6b45fac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="media-encoder-standard-schema"></a>Esquema de codificador de multimédia Standard
Este tópico descreve alguns dos elementos e tipos de esquema XML nos quais [predefinições codificador de multimédia Standard](media-services-mes-presets-overview.md) baseiam-se. O tópico fornece explicações elementos e os respetivos valores válidos. O esquema completo será publicado numa data posterior.  

## <a name="Preset"></a>Predefinição (elemento de raiz)
Define uma predefinição de codificação.  

### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Codificação** |[Codificação](media-services-mes-schema.md#Encoding) |Elemento de raiz, indica que as origens de entrada estão a ser codificado. |
| **Saídas** |[Saídas](media-services-mes-schema.md#Output) |Coleção de ficheiros de saída pretendidas. |

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Versão**<br/><br/> Necessário |**xs:decimal** |A versão predefinida. Aplicam as seguintes restrições: xs:fractionDigits valor = "1" e xs:minInclusive value = "1" por exemplo, **versão = "1.0"**. |

## <a name="Encoding"></a>Codificação
Contém uma sequência dos seguintes elementos.  

### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Definições para 264 codificação de vídeo. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Definições para AAC codificação de áudio. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Definições para a imagem de Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Definições de imagem Png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Definições de imagem Jpg. |

## <a name="H264Video"></a>H264Video
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs = "0" |**xs:Boolean** |Atualmente, passagem de um só é suportada a codificação. |
| **KeyFrameInterval**<br/><br/> minOccurs = "0"<br/><br/> **predefinição = "00: 00:02"** |**xs:Time** |Determina o espaçamento fixo frames Jumbo IDR em unidades de segundos. Também referida como a duração de GOP. Consulte **SceneChangeDetection** (abaixo) para controlar se o codificador pode desvio a partir deste valor. |
| **SceneChangeDetection**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "false" |**xs:Boolean** |Se definido como VERDADEIRO, codificador tenta detetar alteração de cenas as vídeo e insere uma moldura IDR. |
| **Complexidade**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "Equilibrado" |**xs:String** |Controla o compromisso entre codificar qualidade de velocidade e o vídeo. Pode ser um dos seguintes valores: **velocidade**, **equilibrado**, ou **qualidade**<br/><br/> Predefinição: **balanceamento** |
| **SyncMode**<br/><br/> minOccurs = "0" | |Funcionalidade ficará exposta num futuros lançamentos. |
| **H264Layers**<br/><br/> minOccurs = "0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Coleção de camadas de vídeo de saída. |

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:String** | Quando a entrada não tem nenhum vídeo, poderá pretender forçar o codificador de inserir um monochrome controlar de vídeo. Para tal, utilize condição = "InsertBlackIfNoVideoBottomLayerOnly" (para inserir um vídeo, apenas a mais baixa de velocidade de transmissão) ou condição = "InsertBlackIfNoVideo" (inserir um vídeo em todos os saída de forma). Para obter mais informações, veja [este](media-services-advanced-encoding-with-mes.md#no_video) tópico.|

## <a name="H264Layers"></a>H264Layers

Por predefinição, se enviar uma entrada para o codificador que contém apenas de áudio e não vídeo, o elemento de saída irá conter ficheiros com dados de áudio apenas. Alguns jogadores poderão não ser capazes de lidar com esses fluxos de saída. Pode utilizar o H264Video **InsertBlackIfNoVideo** atributo definição para forçar o codificador para adicionar um registo de vídeo à saída de nesse cenário. Para obter mais informações, veja [este](media-services-advanced-encoding-with-mes.md#no_video) tópico.
              
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = maxOccurs "0" = "unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Uma coleção de camadas de H264. |

## <a name="H264Layer"></a>H264Layer
> [!NOTE]
> Os limites de vídeos baseiam-se nos valores descritos no [H264 níveis](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tabela.  
> 
> 

### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "Auto" |**xs:String** |Um dos seguintes podem ser **xs:string** valores: **automática**, **linha de base**, **Main**, **elevada**. |
| **Nível**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "Auto" |**xs:String** | |
| **Velocidade de transmissão**<br/><br/> minOccurs = "0" |**xs:int** |A velocidade de transmissão utilizada para esta camada de vídeo, especificada em kbps. |
| **MaxBitrate**<br/><br/> minOccurs = "0" |**xs:int** |A velocidade de transmissão máxima utilizada para esta camada de vídeo, especificada em kbps. |
| **BufferWindow**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "00: 00:05" |**xs:Time** |Comprimento da memória intermédia de vídeo. |
| **Largura**<br/><br/> minOccurs = "0" |**xs:int** |Largura da moldura vídeo saída, em pixels.<br/><br/> Tenha em atenção que, atualmente, tem de especificar a largura e altura. A largura e altura tem de ser par de números. |
| **Altura**<br/><br/> minOccurs = "0" |**xs:int** |Altura da moldura vídeo saída, em pixels.<br/><br/> Tenha em atenção que, atualmente, tem de especificar a largura e altura. A largura e altura tem de ser par de números.|
| **BFrames**<br/><br/> minOccurs = "0" |**xs:int** |Número de fotogramas B entre frames de referência. |
| **ReferenceFrames**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "3" |**xs:int** |Número de fotogramas de referência num GOP. |
| **EntropyMode**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "Cabac" |**xs:String** |Pode ser um dos seguintes valores: **Cabac** e **Cavlc**. |
| **FrameRate**<br/><br/> minOccurs = "0" |número de Rational |Determina a velocidade de moldura da vídeo de saída. Utilize predefinição "0/1" para permitir que o codificador de utilizar a mesma velocidade de fotogramas como o vídeo de entrada. Valores permitidos são esperados taxas de moldura vídeo comuns, como mostrado abaixo. No entanto, quaisquer válido razoável é permitida. Por exemplo 1/1 seria 1 fps e é válido.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> 24000/1001 (23.976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 fps)<br/><br/> 30000/1001 (29.97 fps) <br/> <br/>**Tenha em atenção** se estiver a criar uma predefinição personalizada para vários múltipla codificação, em seguida, todas as camadas da predefinição **tem** utilizar o mesmo valor de FrameRate.|
| **AdaptiveBFrame**<br/><br/> minOccurs = "0" |**xs:Boolean** |Copiar do codificador de multimédia do Azure |
| **Setores**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "0" |**xs:int** |Determina quantos setores uma moldura está dividida em. Recomendamos a utilizar a predefinição. |

## <a name="AACAudio"></a>AACAudio
 Contém uma sequência dos seguintes elementos e grupos.  

 Para obter mais informações sobre AAC, consulte [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs = "0"<br/><br/> predefinição = "AACLC" |**xs:String** |Pode ser um dos seguintes valores: **AACLC**, **HEAACV1**, ou **HEAACV2**. |

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:String** |Para forçar o codificador para produzir um elemento que contenha um registo de áudio automática quando a entrada não tem nenhum áudio, especifique o valor de "InsertSilenceIfNoAudio".<br/><br/> Por predefinição, se enviar uma entrada para o codificador que contém apenas os vídeos e nenhum áudio, em seguida, o elemento de saída irá conter ficheiros que contêm as vídeos apenas dados. Alguns jogadores poderão não ser capazes de lidar com esses fluxos de saída. Pode utilizar esta definição para forçar o codificador para adicionar um registo de áudio automática para a saída nesse cenário. |

### <a name="groups"></a>Grupos
| Referência | Descrição |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs = "0" |Ver Descrição [AudioGroup](media-services-mes-schema.md#AudioGroup) saber o número apropriado de canais, frequência de amostragem e velocidade de bits que pode ser definida para cada perfil. |

## <a name="AudioGroup"></a>AudioGroup
Para obter detalhes sobre quais os valores são válidos para cada perfil, consulte a tabela de "Detalhes de codec de áudio" que se segue.  

### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Canais**<br/><br/> minOccurs = "0" |**xs:int** |O número de canais de áudio codificado. Seguem-se as opções válidas: 1, 2, 5, 6, 8.<br/><br/> Predefinição: 2. |
| **SamplingRate**<br/><br/> minOccurs = "0" |**xs:int** |A frequência de amostragem de áudio, especificada no Hz. |
| **Velocidade de transmissão**<br/><br/> minOccurs = "0" |**xs:int** |A velocidade de transmissão utilizada quando a codificação de áudio, especificado em kbps. |

### <a name="audio-codec-details"></a>Detalhes de áudio codec
Codec de áudio|Detalhes  
-----------------|---  
**AACLC**|1:<br/><br/> -11025: 8 &lt;= velocidade de transmissão &lt; 16<br/><br/> -12000: 8 &lt;= velocidade de transmissão &lt; 16<br/><br/> -16000: 8 &lt;= velocidade de transmissão &lt;32<br/><br/>-22050: 24 &lt;= velocidade de transmissão &lt; 32<br/><br/> -24000: 24 &lt;= velocidade de transmissão &lt; 32<br/><br/> -32000: 32 &lt;= velocidade de transmissão &lt;= 192<br/><br/> -44100: 56 &lt;= velocidade de transmissão &lt;= 288<br/><br/> -48000: 56 &lt;= velocidade de transmissão &lt;= 288<br/><br/> -88200: 128 &lt;= velocidade de transmissão &lt;= 288<br/><br/> -96000: 128 &lt;= velocidade de transmissão &lt;= 288<br/><br/> 2:<br/><br/> -11025: 16 &lt;= velocidade de transmissão &lt; 24<br/><br/> -12000: 16 &lt;= velocidade de transmissão &lt; 24<br/><br/> -16000: 16 &lt;= velocidade de transmissão &lt; 40<br/><br/> -22050: 32 &lt;= velocidade de transmissão &lt; 40<br/><br/> -24000: 32 &lt;= velocidade de transmissão &lt; 40<br/><br/> -32000: 40 &lt;= velocidade de transmissão &lt;= 384<br/><br/> -44100: 96 &lt;= velocidade de transmissão &lt;= 576<br/><br/> -48000: 96 &lt;= velocidade de transmissão &lt;= 576<br/><br/> -88200: 256 &lt;= velocidade de transmissão &lt;= 576<br/><br/> -96000: 256 &lt;= velocidade de transmissão &lt;= 576<br/><br/> 5/6:<br/><br/> -32000: 160 &lt;= velocidade de transmissão &lt;= 896<br/><br/> -44100: 240 &lt;= velocidade de transmissão &lt;= 1024<br/><br/> -48000: 240 &lt;= velocidade de transmissão &lt;= 1024<br/><br/> -88200: 640 &lt;= velocidade de transmissão &lt;= 1024<br/><br/> -96000: 640 &lt;= velocidade de transmissão &lt;= 1024<br/><br/> 8:<br/><br/> -32000: 224 &lt;= velocidade de transmissão &lt;= 1024<br/><br/> -44100: 384 &lt;= velocidade de transmissão &lt;= 1024<br/><br/> -48000: 384 &lt;= velocidade de transmissão &lt;= 1024<br/><br/> -88200: 896 &lt;= velocidade de transmissão &lt;= 1024<br/><br/> -96000: 896 &lt;= velocidade de transmissão &lt;= 1024  
**HEAACV1**|1:<br/><br/> -22050: velocidade de transmissão = 8<br/><br/> -24000: 8 &lt;= velocidade de transmissão &lt;= 10<br/><br/> -32000: 12 &lt;= velocidade de transmissão &lt;= 64<br/><br/> -44100: 20 &lt;= velocidade de transmissão &lt;= 64<br/><br/> -48000: 20 &lt;= velocidade de transmissão &lt;= 64<br/><br/> -88200: velocidade de transmissão = 64<br/><br/> 2:<br/><br/> -32000: 16 &lt;= velocidade de transmissão &lt;= 128<br/><br/> -44100: 16 &lt;= velocidade de transmissão &lt;= 128<br/><br/> -48000: 16 &lt;= velocidade de transmissão &lt;= 128<br/><br/> -88200: 96 &lt;= velocidade de transmissão &lt;= 128<br/><br/> -96000: 96 &lt;= velocidade de transmissão &lt;= 128<br/><br/> 5/6:<br/><br/> -32000: 64 &lt;= velocidade de transmissão &lt;= 320<br/><br/> -44100: 64 &lt;= velocidade de transmissão &lt;= 320<br/><br/> -48000: 64 &lt;= velocidade de transmissão &lt;= 320<br/><br/> -88200: 256 &lt;= velocidade de transmissão &lt;= 320<br/><br/> -96000: 256 &lt;= velocidade de transmissão &lt;= 320<br/><br/> 8:<br/><br/> -32000: 96 &lt;= velocidade de transmissão &lt;= 448<br/><br/> -44100: 96 &lt;= velocidade de transmissão &lt;= 448<br/><br/> -48000: 96 &lt;= velocidade de transmissão &lt;= 448<br/><br/> -88200: 384 &lt;= velocidade de transmissão &lt;= 448<br/><br/> -96000: 384 &lt;= velocidade de transmissão &lt;= 448  
**HEAACV2**|2:<br/><br/> -22050: 8 &lt;= velocidade de transmissão &lt;= 10<br/><br/> -24000: 8 &lt;= velocidade de transmissão &lt;= 10<br/><br/> -32000: 12 &lt;= velocidade de transmissão &lt;= 64<br/><br/> -44100: 20 &lt;= velocidade de transmissão &lt;= 64<br/><br/> -48000: 20 &lt;= velocidade de transmissão &lt;= 64<br/><br/> -88200: 64 &lt;= velocidade de transmissão &lt;= 64  
  

## <a name="Clip"></a>Clip
### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **StartTime** |**xs: DURATION** |Especifica a hora de início de uma apresentação. O valor de StartTime tem de corresponder aos carimbos absolutos do vídeo de entrada. Por exemplo, se o primeiro intervalo do vídeo de entrada tem um carimbo de 12:00:10.000, em seguida, StartTime deve ser, pelo menos, 12:00:10.000 ou superior. |
| **Duração** |**xs: DURATION** |Especifica a duração de uma apresentação (por exemplo, o aspeto de uma sobreposição no vídeo). |

## <a name="Output"></a>Saída
### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome de ficheiro** |**xs:String** |O nome do ficheiro de saída.<br/><br/> Pode utilizar macros descritas na tabela seguinte para criar os nomes de ficheiro de saída. Por exemplo:<br/><br/> **"Saídas": [{"FileName": "{Basename}*{resolução}*mp4 {de velocidade de transmissão}", "Format": {"Type": "MP4Format"}}] * * |

### <a name="macros"></a>Macros
| Macro | Descrição |
| --- | --- |
| **{Basename}** |Se está a fazer VoD codificação, {Basename} é os primeiro 32 carateres da propriedade AssetFile.Name do ficheiro principal no recurso de entrada.<br/><br/> Se o elemento de entrada é um arquivo em direto, em seguida, o {Basename} deriva os atributos de trackName no manifesto de servidor. Se submeter uma tarefa de subclip utilizar TopBitrate, como: "< VideoStream\>TopBitrate < / VideoStream\>" e o ficheiro de saída contém as vídeo, em seguida, {Basename} é os primeiro 32 carateres de trackName da camada vídeo com velocidade de transmissão mais elevada.<br/><br/> Se em vez disso, são submete uma tarefa de subclip utilizar todos os de forma a entrada, tais como "< VideoStream\>* < / VideoStream\>" e o ficheiro de saída contém as vídeo, em seguida, {Basename} é os primeiro 32 carateres de trackName da camada de vídeo correspondente. |
| **{Codec}** |Mapeia para "H264" para as vídeo e "AAC" de áudio. |
| **{Velocidade de transmissão}** |A destino vídeo velocidade de transmissão se o ficheiro de saída contém vídeo e áudio ou velocidade de transmissão de áudio de destino se o ficheiro de saída contém apenas áudio. O valor utilizado é a velocidade de transmissão em kbps. |
| **{Canal}** |Contagem de canal de áudio se o ficheiro contiver áudio. |
| **{Largura}** |Largura do vídeo, em pixels, no ficheiro de saída, se o ficheiro contém as vídeo. |
| **{Altura}** |Altura do vídeo, em pixels, no ficheiro de saída, se o ficheiro contém as vídeo. |
| **{Extensão}** |Herda a propriedade "Type" para o ficheiro de saída. O nome de ficheiro de saída tem uma extensão que é uma das: "mp4", "ts", "jpg", "png" ou "bmp". |
| **{Índice}** |É obrigatória para a miniatura. Só devem estar presentes uma vez. |

## <a name="Video"></a>Vídeo (tipo complexo herda Codec)
### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Começar** |**xs:String** | |
| **Passo** |**xs:String** | |
| **Intervalo** |**xs:String** | |
| **PreserveResolutionAfterRotation** |**xs:Boolean** |Para obter explicações detalhadas, consulte a secção seguinte: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a>PreserveResolutionAfterRotation
Recomenda-se para utilizar o sinalizador de PreserveResolutionAfterRotation em combinação com os valores de resolução expressados em termos de percentagem (largura = "100%", altura = "100%").  

Por predefinição, as definições de resolução de codificar (largura, altura) nas predefinições de suporte de dados codificador padrão (MES) destinam-se vídeos com o grau de 0 rotação. Por exemplo, se o seu vídeo de entrada é 1280 x 720 com zero rotação de grau, em seguida, as predefinições de predefinição Certifique-se que o resultado a resolução do mesma. Ver a imagem abaixo.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

No entanto, isto significa que se as vídeo de entrada captura com rotação de diferente de zero (ex. um smartphone ou tablet contido verticalmente), em seguida, irá aplicar as definições de resolução de codificar (largura, altura) para o vídeo de entrada MES por predefinição e, em seguida, compensar a rotação. Por exemplo, consulte a imagem abaixo. A predefinição utiliza largura = "100%", altura = "100%", o qual MES interpreta como necessitando de saída 1280 pixels ampla e 720 pixels altura. Após rodar o vídeo, em seguida, diminui a imagem para se ajustar nessa janela, resultando em áreas pillar-box à esquerda e direita.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Se o procedimento acima não é o comportamento pretendido, em seguida, pode efetuar a utilizar o sinalizador de PreserveResolutionAfterRotation e defina-o "true" (a predefinição é "false"). Por isso, se sua predefinidas tem largura = "100%", altura = "100%" PreserveResolutionAfterRotation definido como "true", as vídeo de entrada que é 1280 pixels ampla e 720 pixels talar com rotação de 90 graus irão produzir uma saída com rotação zero grau, mas 720 pixels ampla e 1280 pixels altura. Ver a imagem abaixo.  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a>FormatGroup (agrupar)
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a>BmpLayer
### <a name="element"></a>Elemento
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Largura**<br/><br/> minOccurs = "0" |**xs:int** | |
| **Altura**<br/><br/> minOccurs = "0" |**xs:int** | |

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:String** | |

## <a name="PngLayer"></a>PngLayer
### <a name="element"></a>Elemento
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Largura**<br/><br/> minOccurs = "0" |**xs:int** | |
| **Altura**<br/><br/> minOccurs = "0" |**xs:int** | |

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:String** | |

## <a name="JpgLayer"></a>JpgLayer
### <a name="element"></a>Elemento
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Largura**<br/><br/> minOccurs = "0" |**xs:int** | |
| **Altura**<br/><br/> minOccurs = "0" |**xs:int** | |
| **Qualidade**<br/><br/> minOccurs = "0" |**xs:int** |Os valores válidos: 1(worst)-100(best) |

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:String** | |

## <a name="PngLayers"></a>PngLayers
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = maxOccurs "0" = "unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a>BmpLayers
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = maxOccurs "0" = "unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a>JpgLayers
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = maxOccurs "0" = "unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a>BmpImage (tipo complexo herda de vídeo)
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="JpgImage"></a>JpgImage (tipo complexo herda de vídeo)
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="PngImage"></a>PngImage (tipo complexo herda de vídeo)
### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="examples"></a>Exemplos
Veja exemplos de predefinições XML, que são criadas com base neste esquema, consulte [predefinições de tarefas para MES (codificador de multimédia Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

