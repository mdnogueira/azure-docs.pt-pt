---
title: Esquema de entrada de metadados de Media Services do Azure | Microsoft Docs
description: "O tópico fornece uma descrição geral do esquema da entrada de metadados de Media Services do Azure."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: d72848e2-4b65-4c84-94bc-e2a90a6e7f47
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 4787e4033e1afda6339b0b917263ecc165e400ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="input-metadata"></a>Metadados de entrada
Uma tarefa de codificação está associada a um recurso de entrada (ou ativos) no qual pretende efetuar algumas tarefas de codificação.  Após a conclusão de uma tarefa, é produzido um elemento de saída.  O elemento de saída contém as vídeo, áudio, as miniaturas, manifesto, etc. O elemento de saída também contém um ficheiro com os metadados sobre o recurso de entrada. O nome do ficheiro XML de metadados tem o seguinte formato: &lt;asset_id&gt;_metadata.xml (por exemplo, 41114ad3-eb5e - 4c 57 8d 92-5354e2b7d4a4_metadata.xml), onde &lt;asset_id&gt; valor AssetId o recurso de entrada.  

Se pretender examinar o ficheiro de metadados, pode criar um **SAS** localizador e transferir o ficheiro para o seu computador local. Pode encontrar um exemplo sobre como criar um localizador SAS e transferir um ficheiro [utilizando as extensões de SDK do .NET de serviços de suporte de dados](media-services-dotnet-get-started.md).  

Este tópico descreve os elementos e tipos de esquema XML em que a entrada metada (&lt;asset_id&gt;_metadata.xml) baseia-se.  Para obter informações sobre o ficheiro que contém metadados sobre o elemento de saída, consulte [metadados de saída](media-services-output-metadata-schema.md).  

> [!NOTE]
> Pode encontrar o [esquema código](media-services-input-metadata-schema.md#code) um [exemplo XML](media-services-input-metadata-schema.md#xml) no final deste tópico.  
> 
> 

## <a name="AssetFiles"></a>Elemento de AssetFiles (elemento de raiz)
Contém uma coleção de [AssetFile elemento](media-services-input-metadata-schema.md#AssetFile)s para a tarefa de codificação.  

Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

| Nome | Descrição |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = maxOccurs "1" = "unbounded" |Um elemento subordinado único. Para obter mais informações, consulte [AssetFile elemento](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a>Elemento AssetFile
 Contém atributos e elementos que descrevem um ficheiro de recurso.  

 Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome**<br /><br /> Necessário |**xs:String** |Nome de ficheiro do recurso. |
| **Tamanho**<br /><br /> Necessário |**xs:Long** |Tamanho do ficheiro de recurso em bytes. |
| **Duração**<br /><br /> Necessário |**xs: DURATION** |Duração de back-play conteúdo. Exemplo: Duration = "PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Necessário |**xs:int** |Número de fluxos no ficheiro de recurso. |
| **FormatNames**<br /><br /> Necessário |**xs:String** |Nomes de formato. |
| **FormatVerboseNames**<br /><br /> Necessário |**xs:String** |Formato de nomes verbosos. |
| **StartTime** |**xs: DURATION** |Hora de início de conteúdo. Exemplo: StartTime = "PT2.669S". |
| **OverallBitRate** |**xs:int** |Velocidade de transmissão média do ficheiro de recurso em kbps. |

> [!NOTE]
> Os seguintes elementos 4 subordinados têm de aparecer numa sequência.  
> 
> 

### <a name="child-elements"></a>Elementos subordinados
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Programas**<br /><br /> minOccurs = "0" | |Coleção de todos os [elemento programas](media-services-input-metadata-schema.md#Programs) quando o ficheiro de elemento está no formato de MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs = "0" | |Cada ficheiro de recurso físico pode conter zero ou mais controla vídeo interleaved para um formato de contentor adequado. Este elemento contém uma coleção de todos os [VideoTracks elemento](media-services-input-metadata-schema.md#VideoTracks) que fazem parte do ficheiro do elemento. |
| **AudioTracks**<br /><br /> minOccurs = "0" | |Cada ficheiro de recurso físico pode conter zero ou mais controla áudio interleaved para um formato de contentor adequado. Este elemento contém uma coleção de todos os [AudioTracks elemento](media-services-input-metadata-schema.md#AudioTracks) que fazem parte do ficheiro do elemento. |
| **Metadados**<br /><br /> minOccurs = maxOccurs "0" = "unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Metadados de ficheiro de elemento representado como key\value cadeias. Por exemplo:<br /><br /> **&lt;Chave de metadados = "idioma" value = "eng" /&gt;** |

## <a name="TrackType"></a>TrackType
Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **ID**<br /><br /> Necessário |**xs:int** |Índice baseado em zero de controlar este vídeo ou áudio.<br /><br /> Não é necessariamente que o TrackID como utilizada num ficheiro MP4. |
| **Codec** |**xs:String** |Cadeia de codec controlar vídeo. |
| **CodecLongName** |**xs:String** |Codec de vídeo ou áudio controlar longa nome. |
| **Base temporal**<br /><br /> Necessário |**xs:String** |Base para tempo. Exemplo: Base-temporal = "1/48000" |
| **NumberOfFrames** |**xs:int** |Número de fotogramas (presentes para controla vídeo). |
| **StartTime** |**xs: DURATION** |Hora de início de controlar. Exemplo: StartTime = "PT2.669S" |
| **Duração** |**xs: DURATION** |Controlar a duração. Exemplo: Duration = "PTSampleFormat M37.757S". |

> [!NOTE]
> Os seguintes elementos 2 subordinados têm de aparecer numa sequência.  
> 
> 

### <a name="child-elements"></a>Elementos subordinados
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Disposição**<br /><br /> minOccurs = maxOccurs "0" = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Contém informações de apresentação (por exemplo, se um determinado controlar áudio destina visualizadores visualmente debilitadas). |
| **Metadados**<br /><br /> minOccurs = maxOccurs "0" = "unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Cadeias de genérico chave/valor que podem ser utilizadas para conter uma variedade de informações. Por exemplo, chave = "idioma" e o valor = "eng". |

## <a name="AudioTrackType"></a>AudioTrackType (herda TrackType)
 **AudioTrackType** é um tipo complexo global que herda de [TrackType](media-services-input-metadata-schema.md#TrackType).  

 O tipo de representa um registo de áudio específico no ficheiro de recurso.  

 Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **SampleFormat** |**xs:String** |Formato de exemplo. |
| **ChannelLayout** |**xs:String** |Esquema de canal. |
| **Canais**<br /><br /> Necessário |**xs:int** |Número (0 ou mais) de canais de áudio. |
| **SamplingRate**<br /><br /> Necessário |**xs:int** |Frequência de amostragem de áudio em amostras/seg ou Hz. |
| **Velocidade de transmissão** |**xs:int** |Taxa de bits de áudio médio em bits por segundo, conforme calculada a partir do ficheiro de recurso. O payload de fluxo de estatísticas básicas é contabilizado e a sobrecarga de empacotamento não está incluída nesta contagem. |
| **BitsPerSample** |**xs:int** |Bits por exemplo, para o formato de wFormatTag escreva. |

## <a name="VideoTrackType"></a>VideoTrackType (herda TrackType)
**VideoTrackType** é um tipo complexo global que herda de [TrackType](media-services-input-metadata-schema.md#TrackType).  

O tipo de representa um registo de vídeo específico no ficheiro de recurso.  

Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **FourCC**<br /><br /> Necessário |**xs:String** |Codec vídeo FourCC código. |
| **Perfil** |**xs:String** |Perfil de vídeo controlar. |
| **Nível** |**xs:String** |Nível de vídeo controlar. |
| **PixelFormat** |**xs:String** |Formato de pixel de vídeo controlar. |
| **Largura**<br /><br /> Necessário |**xs:int** |Codificado as vídeo largura em pixéis. |
| **Altura**<br /><br /> Necessário |**xs:int** |Altura de vídeo codificada em pixels. |
| **DisplayAspectRatioNumerator**<br /><br /> Necessário |**xs:Double** |Vídeo proporção numerator. |
| **DisplayAspectRatioDenominator**<br /><br /> Necessário |**xs:Double** |Denominador de proporção de vídeo. |
| **DisplayAspectRatioDenominator**<br /><br /> Necessário |**xs:Double** |Numerator de proporção de exemplo de vídeo. |
| **SampleAspectRatioNumerator** |**xs:Double** |Numerator de proporção de exemplo de vídeo. |
| **SampleAspectRatioNumerator** |**xs:Double** |Denominador de proporção de exemplo de vídeo. |
| **FrameRate**<br /><br /> Necessário |**xs:decimal** |Taxa de moldura de vídeo no formato .3f de medida. |
| **Velocidade de transmissão** |**xs:int** |Taxa de bits de vídeo médio no kilobits por segundo, conforme calculada a partir do ficheiro de recurso. O payload de fluxo de estatísticas básicas é contabilizado e a sobrecarga de empacotamento não está incluída. |
| **MaxGOPBitrate** |**xs:int** |Máx. GOP média velocidade de transmissão para este vídeo controlar, no kilobits por segundo. |
| **HasBFrames** |**xs:int** |Vídeo de controlar o número de fotogramas B. |

## <a name="MetadataType"></a>MetadataType
**MetadataType** é um tipo complexo global que descreve os metadados de um ficheiro de recurso como cadeias de chave/valor. Por exemplo, chave = "idioma" e o valor = "eng".  

Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **chave**<br /><br /> Necessário |**xs:String** |A chave na par chave/valor. |
| **valor**<br /><br /> Necessário |**xs:String** |O valor no par chave/valor. |

## <a name="ProgramType"></a>ProgramType
**ProgramType** é um tipo complexo global que descreve um programa.  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **ProgramId**<br /><br /> Necessário |**xs:int** |Id do programa |
| **NumberOfPrograms**<br /><br /> Necessário |**xs:int** |Número de programas. |
| **PmtPid**<br /><br /> Necessário |**xs:int** |Tabelas de mapa do programa (PMTs) contêm informações sobre programas.  Para obter mais informações, consulte [PMt](http://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Necessário |**xs:int** |Utilizado por descodificador. Para obter mais informações, consulte [PCR](http://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: longo** |A iniciar o carimbo de hora de apresentação. |
| **EndPTS** |**xs: longo** |Carimbo de hora de apresentação de terminar. |

## <a name="StreamDispositionType"></a>StreamDispositionType
**StreamDispositionType** é um tipo complexo global que descreve o fluxo.  

Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Predefinição**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que esta é a apresentação predefinida. |
| **Dub**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que esta é a apresentação dubbed. |
| **Original**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que esta é a apresentação original. |
| **Comentário**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que este registo contém interrompa. |
| **Lyrics**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que este registo contém lyrics. |
| **Karaoke**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que isto representa a controlar karaoke (leitores de música em segundo plano, sem vocals). |
| **Forçada**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que esta é a apresentação forçada. |
| **HearingImpaired**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar este controlar para hearing danificada. |
| **VisualImpaired**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que este controlar destina visualmente afetada. |
| **CleanEffects**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que este controlar tem efeitos limpo. |
| **AttachedPic**<br /><br /> Necessário |**xs:int** |Defina este atributo como 1 para indicar que este controlar tem imagens. |

## <a name="Programs"></a>Elemento de programas
Elemento wrapper que contém vários **programa** elementos.  

### <a name="child-elements"></a>Elementos subordinados
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Programa**<br /><br /> minOccurs = maxOccurs "0" = "unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Para ficheiros de recurso que estão no formato de MPEG-TS, contém informações sobre programas no ficheiro de recurso. |

## <a name="VideoTracks"></a>Elemento VideoTracks
 Elemento wrapper que contém vários **VideoTrack** elementos.  

 Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementos subordinados
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = maxOccurs "0" = "unbounded" |[VideoTrackType (herda TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Contém informações sobre as vídeo controla no ficheiro de recurso. |

## <a name="AudioTracks"></a>Elemento AudioTracks
 Elemento wrapper que contém vários **AudioTrack** elementos.  

 Ver um exemplo XML no final deste tópico: [exemplo XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elementos
| Nome | Tipo | Descrição |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs = maxOccurs "0" = "unbounded" |[AudioTrackType (herda TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Contém informações sobre controla áudio no ficheiro de recurso. |

## <a name="code"></a>Código de esquema
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml"></a>Exemplo XML
Segue-se um exemplo do ficheiro de metadados da entrada.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

