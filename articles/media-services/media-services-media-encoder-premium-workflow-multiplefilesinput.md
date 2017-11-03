---
title: "Vários ficheiros de entrada e propriedades do componente com o codificador Premium - Azure | Microsoft Docs"
description: "Este tópico explica como utilizar setRuntimeProperties para utilizar vários ficheiros de entrada e passar dados personalizados para o processador de multimédia de fluxo de trabalho do suporte de dados codificador Premium."
services: media-services
documentationcenter: 
author: xpouyat
manager: cfowler
editor: 
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: df1ee5089a0af6ffce1431b658843fcb34a66ce5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Utilizar vários ficheiros de entrada e propriedades do componente com o codificador Premium
## <a name="overview"></a>Descrição geral
Existem cenários em que poderá ser necessário personalizar propriedades do componente, especifique o XML de lista de Clip conteúdo, ou enviar vários ficheiros de entrada quando submete uma tarefa com o **o fluxo de trabalho do suporte de dados codificador Premium** processador de multimédia. Alguns exemplos incluem:

* Sobrepor texto em vídeo e definindo o valor de texto (por exemplo, a data atual) em tempo de execução para cada vídeo de entrada.
* Personalizar o XML de lista Clip (para especificar um ou vários ficheiros de origem, com ou sem corte, etc.).
* Sobrepor uma imagem de logótipo no vídeo de entrada enquanto o vídeo está codificado.
* Vários a codificação de idioma de áudio.

Para permitir que o **o fluxo de trabalho do suporte de dados codificador Premium** saber que estiver a alterar algumas propriedades no fluxo de trabalho ao criar a tarefa ou enviar vários ficheiros de entrada, tem de utilizar uma cadeia de configuração que contenha **setRuntimeProperties** e/ou **transcodeSource**. Este tópico explica como utilizá-los.

## <a name="configuration-string-syntax"></a>Sintaxe da cadeia de configuração
A cadeia de configuração para definir a tarefa de codificação utiliza um documento XML que tem este aspeto:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

O código de c# que lê a configuração XML a partir de um ficheiro, atualizá-lo com o nome de ficheiro de vídeo à direita e passa-a para a tarefa de uma tarefa é o seguinte:

```c#
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Personalizar propriedades do componente
### <a name="property-with-a-simple-value"></a>Propriedade com um valor simple
Em alguns casos, isto é útil personalizar uma propriedade do componente, juntamente com o ficheiro de fluxo de trabalho que vai ser executado por fluxo de trabalho do suporte de dados codificador Premium.

Suponha concebido um fluxo de trabalho que texto sobrepõe nos seus vídeos e o texto (por exemplo, a data atual) deve ser definido durante a execução. Pode fazê-lo ao enviar o texto a ser definida como o novo valor para a propriedade de texto do componente de sobreposição entre a tarefa de codificação. Pode utilizar este mecanismo para alterar as outras propriedades de um componente no fluxo de trabalho (tais como a posição ou cor da sobreposição, a velocidade de transmissão do codificador AVC, etc.).

**setRuntimeProperties** é utilizada para substituir uma propriedade nos componentes do fluxo de trabalho.

Exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Propriedade com um valor XML
Para definir uma propriedade que espera um valor XML, encapsular utilizando `<![CDATA[ and ]]>`.

Exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Certifique-se de não colocar em espera um avanço retorno apenas após `<![CDATA[`.

### <a name="propertypath-value"></a>valor de propertyPath
Nos exemplos anteriores, o propertyPath foi "/ ficheiro do suporte de dados de entrada/filename" ou "/ inactiveTimeout" ou "clipListXml".
Isto é, em geral, o nome do componente, em seguida, o nome da propriedade. O caminho pode ter mais ou menos níveis, como "/ primarySourceFile" (porque a propriedade na raiz do fluxo de trabalho) ou "/ vídeo sobreposição/opacidade de processamento/gráfico" (porque a sobreposição está num grupo).    

Para verificar o nome de caminho e propriedade, utilize o botão de ação que é imediatamente, ao lado de cada propriedade. Pode clicar neste botão de ação e selecione **editar**. Isto irá mostrar o nome real da propriedade e, imediatamente acima, o espaço de nomes.

![Ação/editar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriedade](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Vários ficheiros de entrada
Cada tarefa que submeter para o **o fluxo de trabalho do suporte de dados codificador Premium** requer dois elementos:

* A primeira é um *elemento de fluxo de trabalho* que contém um ficheiro de fluxo de trabalho. Pode estruturar ficheiros de fluxo de trabalho utilizando o [estruturador de fluxo de trabalho](media-services-workflow-designer.md).
* Segunda é um *recurso de multimédia* que contém os ficheiros de suporte de dados que pretende codificar.

Quando está a enviar vários ficheiros de suporte de dados para o **o fluxo de trabalho do suporte de dados codificador Premium** aplicam do codificador, as seguintes restrições:

* Todos os ficheiros de suporte de dados têm de estar no mesmo *recurso de multimédia*. Não é suportada a utilização de vários recursos de suporte de dados.
* Tem de definir o ficheiro primário neste recurso de multimédia (Idealmente, este é o ficheiro de vídeo principal que o codificador é-lhe pedido para processar).
* É necessário transmitir dados de configuração que inclui o **setRuntimeProperties** e/ou **transcodeSource** elemento para que o processador.
  * **setRuntimeProperties** é utilizada para substituir a propriedade de nome de ficheiro ou de outra propriedade nos componentes do fluxo de trabalho.
  * **transcodeSource** é utilizado para especificar o conteúdo XML de lista Clip.

Ligações no fluxo de trabalho:

* Se utilizar um ou vários componentes de entrada de ficheiro do suporte de dados e se planeia utilizar **setRuntimeProperties** para especificar o nome de ficheiro, em seguida, não ligar o pin de componente de ficheiro primário aos mesmos. Certifique-se de que não há nenhuma ligação entre o objeto de ficheiro primário e o Input(s) de ficheiro do suporte de dados.
* Se preferir utilizar Clip lista XML e um componente de origem de suporte de dados, em seguida, pode ligar ambos em conjunto.

![Nenhuma ligação a partir do ficheiro de origem principal para entrada de ficheiro do suporte de dados](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Não há nenhuma ligação no ficheiro principal para componentes de entrada de ficheiro do suporte de dados se utilizarem setRuntimeProperties para definir a propriedade de nome de ficheiro.*

![Ligação da lista de Clip XML para recortar origem da lista](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Pode ligar Clip lista XML à origem de suporte de dados e utilizar transcodeSource.*

### <a name="clip-list-xml-customization"></a>Recortar personalização do XML de lista
Pode especificar o XML de lista Clip no fluxo de trabalho em tempo de execução utilizando **transcodeSource** na configuração da cadeia XML. Isto requer que o pin do XML de lista Clip estar ligados ao componente de origem de suporte de dados no fluxo de trabalho.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Se pretender especificar /primarySourceFile para utilizar esta propriedade para o nome de ficheiros de saída através de 'Expressões', em seguida, é recomendado passar o XML de lista Clip como uma propriedade *depois* propriedade /primarySourceFile, para evitar que a lista de Clip ser substituído pela definição /primarySourceFile.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Com o corte de moldura exata adicional:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exemplo 1: Sobreposição uma imagem por cima a vídeo

### <a name="presentation"></a>Apresentação
Considere um exemplo em que pretende para a sobreposição de uma imagem de logótipo na vídeo de entrada enquanto o vídeo está codificado. Neste exemplo, o vídeo de entrada é denominado "Microsoft_HoloLens_Possibilities_816p24.mp4" e o logótipo com o nome "logo.png". Deve executar os seguintes passos:

* Criar um elemento de fluxo de trabalho com o ficheiro de fluxo de trabalho (consulte o exemplo seguinte).
* Criar um recurso de suporte de dados, que contém dois ficheiros: MyInputVideo.mp4 como o ficheiro primário e MyLogo.png.
* Enviar uma tarefa para o processador de suporte de dados de fluxo de trabalho do suporte de dados codificador Premium com os recursos de entrada acima e especificar a seguinte cadeia de configuração.

Configuração:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

No exemplo acima, o nome do ficheiro de vídeo é enviado para o componente de entrada de ficheiro do suporte de dados e a propriedade primarySourceFile. O nome do ficheiro de logótipo é enviado para outra entrada do ficheiro de suporte de dados que está ligada ao componente de sobreposição de gráfico.

> [!NOTE]
> O nome de ficheiro de vídeo é enviado para a propriedade primarySourceFile. O motivo para isto é para utilizar esta propriedade no fluxo de trabalho para criar o nome de ficheiro de saída correto utilizando expressões, por exemplo.

### <a name="step-by-step-workflow-creation"></a>Criação de fluxo de trabalho passo a passo
Eis os passos para criar um fluxo de trabalho que demora dois ficheiros como entrada: um vídeo e uma imagem. Este será sobreposição a imagem por cima a vídeo.

Abra **estruturador de fluxo de trabalho** e selecione **ficheiro** > **nova área de trabalho** > **transcodificar Blueprint**.

O novo fluxo de trabalho mostra três elementos:

* Ficheiro de origem principal
* Lista de clip XML
* Ficheiro/elemento de saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Novo fluxo de trabalho de codificação*

Para aceitar o ficheiro de suporte de dados de entrada, comece por adicionar um componente de entrada de ficheiro do suporte de dados. Para adicionar um componente para o fluxo de trabalho, procure na caixa de pesquisa de repositório e arraste a entrada pretendida para o painel do estruturador.

Em seguida, adicione o ficheiro de vídeo a ser utilizado para estruturar o fluxo de trabalho. Para tal, clique no painel de fundo no estruturador de fluxo de trabalho e procure a propriedade de ficheiro de origem principal no painel da direita de propriedade. Clique no ícone de pastas e selecione o ficheiro de vídeo adequado.

![Origem de ficheiro primário](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Origem de ficheiro primário*

Em seguida, especifique o ficheiro de vídeo no componente de entrada de ficheiro do suporte de dados.   

![Origem de entrada do ficheiro de multimédia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Origem de entrada do ficheiro de multimédia*

Assim que este procedimento é efetuado, o componente de entrada de ficheiro do suporte de dados irá inspecionar o ficheiro e preencher os pins de saída para refletir o ficheiro que-inspecionado.

O passo seguinte consiste em Adicionar um "vídeo dados tipo Atualizador" para especificar o espaço de cor para Rec.709. Adicionar um "vídeo formato conversor" que está definido como tipo de dados. o esquema/esquema = Planar configurável. Isto irá converter o fluxo de vídeo num formato que pode ser executada como uma origem do componente de sobreposição.

![vídeo Atualizador do tipo de dados e conversor de formato](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Atualizador de tipo de dados de vídeo e conversor de formato*

![Tipo de esquema = configuráveis Planar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Tipo de esquema é configurável Planar*

Em seguida, adicione um componente de sobreposição de vídeo e ligar o pin de vídeo (descomprimido) para o pin de vídeo (descomprimido) da entrada de ficheiro do suporte de dados.

Adicionar a entrada do ficheiro de suporte de dados de outro (para carregar o ficheiro de logótipo), clique neste componente e alterá-lo para "Logótipo de entrada do ficheiro de suporte de dados" e selecione uma imagem (um ficheiro PNG por exemplo) na propriedade de ficheiro. Ligar o pin não comprimida da imagem para o pin não comprimida da imagem de sobreposição.

![Origem de ficheiros de componentes e a imagem de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Origem de ficheiros de componentes e a imagem de sobreposição*

Se pretender modificar a posição de logótipo no vídeo (por exemplo, pode querer posicioná-la em 10 por cento retire canto superior esquerdo do vídeo), desmarque a caixa de verificação "Manual de entrada". Pode fazê-lo porque estão a utilizar uma entrada de ficheiro do suporte de dados para fornecer o ficheiro de logótipo para o componente de sobreposição.

![Posição de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posição de sobreposição*

Codificar o fluxo de vídeo para 264, adicione os componentes de codificador codificador vídeo AVC e AAC para a superfície do designer. Ligar os pins.
Configurar o codificador AAC e selecione o formato de áudio conversão/predefinição: 2.0 (L, R).

![Codificadores de áudio e vídeos](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de áudio e vídeos*

Agora adicione o **ISO Mpeg-4 Multiplexer** e **ficheiro de saída** componentes e ligue os pins, conforme mostrado.

![MP4 multiplexer e de saída do ficheiro](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer e de saída do ficheiro*

Tem de definir o nome para o ficheiro de saída. Clique em de **ficheiro de saída** componente e editar a expressão para o ficheiro:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome do ficheiro de saída](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome do ficheiro de saída*

Pode executar o fluxo de trabalho localmente Verifique se está a funcionar corretamente.

Depois de concluído, pode executá-lo no Media Services do Azure.

Em primeiro lugar, prepare um recurso de Media Services do Azure com dois ficheiros: o ficheiro de vídeo e o logótipo. Pode fazê-lo utilizando o .NET ou a REST API. Também pode fazer isto, utilizando o portal do Azure ou [Explorador de serviços de suporte de dados do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Este tutorial mostra como gerir recursos com AMSE. Existem duas formas de adicionar ficheiros a um recurso:

* Crie uma pasta local, copie os dois ficheiros na mesma e arrastar e largar a pasta para o **Asset** separador.
* Carregar o ficheiro de vídeo como um recurso, apresenta as informações de recurso, aceda ao separador ficheiros e carregar um ficheiro adicional (logótipo).

> [!NOTE]
> Certifique-se definir um ficheiro primário no elemento (vídeo ficheiro principal).

![Ficheiros de recurso na AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Ficheiros de recurso na AMSE*

Selecione o elemento e optar por codificá-lo com o codificador Premium. Carregar o fluxo de trabalho e selecione-o.

Clique no botão para transmitir dados para o processador e adicione o seguinte XML para definir as propriedades do tempo de execução:

![Codificador Premium no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificador Premium no AMSE*

Em seguida, cole os seguintes dados XML. Tem de especificar o nome do ficheiro de vídeo de entrada de ficheiro do suporte de dados e primarySourceFile. Especifique o nome do nome do ficheiro para o logótipo demasiado.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Se utilizar o SDK .NET para criar e executar a tarefa, estes dados XML tem de ser transmitida como a cadeia de configuração.

```c#
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Depois da tarefa estiver concluída, o ficheiro MP4 no elemento de saída apresenta sobreposição!

![Sobreposição no vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sobreposição no vídeo*

Pode transferir o fluxo de trabalho de exemplo do [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exemplo 2: Vários idiomas áudio codificação

Um exemplo de vários idiomas áudio workfkow codificação está disponível no [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Esta pasta contém um fluxo de trabalho de exemplo que pode ser utilizado para codificar um ficheiro MXF a um recurso de ficheiros MP4 de várias com vários controla áudio.

Este fluxo de trabalho parte do princípio de que o ficheiro MXF contém um controlar áudio; a atividade de áudio adicionais deve ser transmitida como ficheiros de áudio separados (WAV ou MP4...).

Codificar, siga estes passos:

* Crie um recurso dos Media Services com o ficheiro MXF e os ficheiros de áudio (ficheiros de áudio 0 para 18).
* Certifique-se de que o ficheiro MXF está definido como um ficheiro primário.
* Crie uma tarefa e uma tarefa com o processador de codificador de fluxo de trabalho Premium. Utilize o fluxo de trabalho fornecido (MultiMP4-1080p-19audio-v1.workflow).
* Transmitir os dados de setruntime.xml à tarefa (se utilizar o Explorador de serviços de suporte de dados do Azure, utilize o botão "transferir dados de xml para o fluxo de trabalho").
  * Atualize os dados XML para especificar as etiquetas de idiomas e nomes de ficheiro corretos.
  * O fluxo de trabalho tem áudio componentes com o nome de áudio 1 para 18 áudio.
  * RFC5646 é suportada para a tag de idioma.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* O elemento codificado irá conter controla de áudio de idioma de várias e estes controla deve ser selecionável no leitor de multimédia do Azure.

## <a name="see-also"></a>Consultar também
* [Introdução às Premium codificação, no suporte de dados do Azure, os serviços](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Como utilizar a Premium codificação de mensagens em fila nos serviços de suporte de dados do Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [A codificação de conteúdo a pedido com Media Services do Azure](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Formatos de fluxo de trabalho do suporte de dados codificador Premium e codecs](media-services-premium-workflow-encoder-formats.md)
* [Ficheiros de fluxo de trabalho de exemplo](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)
* [Ferramenta de Explorador de serviços de suporte de dados do Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
