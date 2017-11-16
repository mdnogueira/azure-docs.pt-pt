---
title: "Como codificar um elemento do Azure utilizando o codificador de multimédia Standard | Microsoft Docs"
description: "Saiba como utilizar o codificador de multimédia Standard para codificar o conteúdo do suporte de dados nos serviços de suporte de dados do Azure. Exemplos de código utilizam a REST API."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: 1622149009a37b864e84caa158da960ccc03ca65
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Como codificar um elemento com o codificador de multimédia Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Descrição geral
Para distribuir vídeo digital através da Internet, tem de comprimir o suporte de dados. Ficheiros de vídeo digitais grandes e poderão ser demasiado grandes para entregar através da Internet, ou para dispositivos dos seus clientes apresentar corretamente. A codificação é o processo de compressão vídeos e áudio, para que os seus clientes podem visualizar o seu suporte de dados.

As tarefas de codificação são uma das operações de processamento mais comuns nos Media Services do Azure. Estes Serviços permitem-lhe criar tarefas de codificação para converter ficheiros de multimédia de uma codificação para outra. Quando codificar, pode utilizar o codificador incorporado de Media Services (codificador de multimédia Standard). Também pode utilizar um codificador fornecido por um parceiro de Media Services. Codificadores de terceiros estão disponíveis através do Azure Marketplace. Pode especificar os detalhes das tarefas de codificação, utilizando cadeias predefinidas definidas para o codificador ou utilizando os ficheiros de configuração predefinidas. Para ver os tipos de predefinições que estão disponíveis, consulte [predefinições de tarefas para o codificador de multimédia Standard](http://msdn.microsoft.com/library/mt269960).

Cada tarefa pode ter uma ou mais tarefas, dependendo do tipo de processamento que pretende efetuar. Através da API REST, pode criar tarefas e as respetivas tarefas relacionadas com uma das seguintes formas:

* Tarefas podem ser definidos inline através da propriedade de navegação de tarefas no entidades de tarefa.
* Utilize o processamento em lote OData.

Recomendamos que codificar sempre os ficheiros de origem para uma conjunto de MP4 de velocidade de transmissão adaptável e, em seguida, converter o conjunto no formato pretendido utilizando [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

Se o elemento de saída é armazenamento encriptado, tem de configurar a política de entrega de elemento. Para obter mais informações, consulte [configurar política de entrega de elemento](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Considerações

Ao aceder a entidades nos Media Services, tem de definir campos de cabeçalho específicos e os valores no seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST de serviços de suporte de dados](media-services-rest-how-to-use.md).

Antes de iniciar os processadores de suporte de dados de referência, certifique-se de que tem o suporte de dados correto ID do processador. Para obter mais informações, consulte [obter os processadores de multimédia](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à API do AMS, consulte [aceder à API de serviços de suporte de dados do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Depois de ligar com êxito a https://media.windows.net, receberá um redirecionamento 301 especificando noutro URI de serviços de suporte de dados. Tem de se as chamadas subsequentes para o novo URI.

## <a name="create-a-job-with-a-single-encoding-task"></a>Criar uma tarefa com uma única tarefa de codificação
> [!NOTE]
> Quando estiver a trabalhar com a API de REST de serviços de suporte de dados, aplicam as seguintes considerações:
>
> Ao aceder a entidades nos Media Services, tem de definir campos de cabeçalho específicos e os valores no seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST dos serviços de suporte de dados](media-services-rest-how-to-use.md).
>
> Depois de ligar com êxito a https://media.windows.net, receberá um redirecionamento 301 especificando noutro URI de serviços de suporte de dados. Tem de se as chamadas subsequentes para o novo URI. Para obter informações sobre como ligar à API do AMS, consulte [aceder à API de serviços de suporte de dados do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md).
>
> Quando utilizando JSON e especificando a utilizar o **METADATA** palavra-chave no pedido (por exemplo, para referencia um objeto ligado), tem de definir o **aceitar** cabeçalho para [formato JSON verboso](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Aceite: application/json; odata = verboso.
>
>

O exemplo seguinte mostra como criar e publicar uma tarefa com uma tarefa definida como codificar um vídeo numa resolução específico e qualidade. Quando codificar com o codificador de multimédia Standard, pode utilizar predefinições de configuração de tarefas especificadas [aqui](http://msdn.microsoft.com/library/mt269960).

Pedido:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Resposta:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Definir nome do elemento de saída
O exemplo seguinte mostra como definir o atributo assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Considerações
* Propriedades de TaskBody tem de utilizar literal XML para definir o número de entrada ou saída de ativos que são utilizados pela tarefa. O tópico de tarefas contém a definição de esquema XML para o XML.
* O valor na definição do TaskBody, para cada interna <inputAsset> e <outputAsset> tem de ser definido como JobInputAsset(value) ou JobOutputAsset(value).
* Uma tarefa pode ter vários recursos de saída. Um JobOutputAsset(x) só pode ser utilizado uma vez como resultado de uma tarefa numa tarefa.
* Pode especificar JobInputAsset ou JobOutputAsset como um recurso de entrada de uma tarefa.
* Tarefas tem não formam um ciclo.
* O parâmetro de valor que passa ao JobInputAsset ou JobOutputAsset representa o valor de índice para um recurso. Os recursos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets na definição de entidade da tarefa.
* Porque os serviços de suporte de dados está incorporado no OData v3, os ativos individuais em coleções de propriedade de navegação InputMediaAssets e OutputMediaAssets referenciados através de um " METADATA: uri" par nome / valor.
* InputMediaAssets mapeia para um ou mais recursos que criou nos Media Services. OutputMediaAssets são criados pelo sistema. Eles não fazem referência um recurso existente.
* OutputMediaAssets pode ter um nome utilizando o atributo assetName. Se este atributo não está presente, em seguida, o nome do OutputMediaAsset é que o valor de texto interno do <outputAsset> elemento está com um sufixo do valor de nome de tarefa ou o valor de Id da tarefa (no caso em que a propriedade de nome não está definida). Por exemplo, se definir um valor para assetName "Exemplo", em seguida, o nome de OutputMediaAsset propriedade está definida como "Exemplo". No entanto, se não definir um valor para assetName, mas definido no nome da tarefa para "NewJob", em seguida, o nome de OutputMediaAsset seria "_NewJob JobOutputAsset (valor)."

## <a name="create-a-job-with-chained-tasks"></a>Criar uma tarefa com tarefas em cadeia
Em vários cenários de aplicação, os programadores pretendem criar uma série de tarefas de processamento. Nos Media Services, pode criar uma série de tarefas em cadeia. Cada tarefa executa os passos de processamento diferente e utiliza processadores de diferentes de suporte de dados. As tarefas em cadeia podem entregam um recurso de uma tarefa para outro, executar uma sequência de tarefas linear no elemento. No entanto, as tarefas executadas numa tarefa não têm de estar numa sequência. Quando cria uma tarefa em cadeia, a em cadeia **ITask** objetos são criados numa única **IJob** objeto.

> [!NOTE]
> Atualmente, não há um limite de 30 tarefas por tarefa. Se precisar de mais de 30 tarefas de encadear, crie mais do que uma tarefa para conter as tarefas.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Considerações
Para ativar o encadeamento de tarefas:

* Uma tarefa tem de ter, pelo menos, duas tarefas.
* Tem de existir pelo menos uma tarefa cuja entrada é o resultado da outra tarefa no trabalho.

## <a name="use-odata-batch-processing"></a>Utilize o processamento em lote OData
O exemplo seguinte mostra como utilizar o processamento em lote OData para criar uma tarefa e tarefas. Para obter informações sobre o processamento em lote, consulte [Open Data Protocol (OData) Batch processamento](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Criar uma tarefa utilizando um JobTemplate
Ao processar vários recursos através de um conjunto comum de tarefas, utilize um JobTemplate para especificar as predefinições de tarefas predefinida ou definir a sequência de tarefas.

O exemplo seguinte mostra como criar um JobTemplate com um TaskTemplate que é definidos inline. Utiliza o codificador de multimédia Standard o TaskTemplate como o MediaProcessor para codificar o ficheiro de recurso. No entanto, outras MediaProcessors podem ser utilizados, bem como.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Ao contrário de outras entidades de Media Services, tem de definir um novo identificador GUID para cada TaskTemplate e coloque-o na propriedade de Id e taskTemplateId no corpo do pedido. O esquema de identificação de conteúdo tem de seguir o esquema descrito em identificar entidades de serviços de suporte de dados do Azure. Além disso, não é possível atualizar JobTemplates. Em vez disso, tem de criar um novo com as suas alterações atualizadas.
>
>

Se tiver êxito, é devolvida a resposta do seguinte:

    HTTP/1.1 201 Created

    . . .


O exemplo seguinte mostra como criar uma tarefa que faça referência a um Id de JobTemplate:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Se tiver êxito, é devolvida a resposta do seguinte:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Funcionalidades de codificação de mensagens em fila avançadas para explorar
* [Como gerar miniaturas](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Gerar miniaturas durante a codificação](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Vídeos de cortar durante a codificação](media-services-crop-video.md)
* [Personalizar predefinições de codificação](media-services-custom-mes-presets-with-dotnet.md)
* [Sobreposição ou um vídeo com uma imagem de marca d'água](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que sabe como criar uma tarefa para codificar um elemento, consulte [como verificar o progresso da tarefa com os Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Consultar também
[Obter os processadores de multimédia](media-services-rest-get-media-processor.md)
