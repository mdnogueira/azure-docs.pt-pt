---
title: Como para utilizar Blitline imagem guia de funcionalidade processamento - Azure
description: "Saiba como utilizar o serviço de Blitline para imagens de processo dentro de uma aplicação do Azure."
services: 
documentationcenter: .net
author: blitline-dev
manager: jason@blitline.com
editor: jason@blitline.com
ms.assetid: 6c711248-0e52-4895-ba9e-8395628de924
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2014
ms.author: support@blitline.com
ms.openlocfilehash: 1d90599e028b3407a513b04b878e3aefc39928a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Como utilizar Blitline com o Azure e o armazenamento do Azure
Este guia irá explicam como aceder aos serviços Blitline e como submeter tarefas ao Blitline.

## <a name="what-is-blitline"></a>O que é Blitline?
Blitline é uma imagem baseado na nuvem de serviço que fornece processamento de imagem de nível empresarial em fração do preço de seria custo compilá-la sozinho de processamento.

O facto é que o processamento de imagens tiver sido realizado repetidas, normalmente, reconstruído a partir do zero para o Web site todas as. Isto Note porque foi compilou-los um vezes milhões demasiado. Um dia, que decidimos que talvez está na altura, apenas fazê-lo para todas as pessoas. Sabemos como fazê-lo, para fazê-lo rápido e eficiente e guardar todas as pessoas escolar entretanto.

Para obter mais informações, consulte [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>O que Blitline não é...
Para esclarecer que é útil para Blitline, muitas vezes, é mais fácil identificar o Blitline não fazer antes de prosseguir.

* Blitline não tem widgets HTML para carregar as imagens. Tem de ter imagens disponíveis publicamente ou com permissões restritas disponíveis para Blitline aceder.
* Não Blitline imagem em direto, processamento, como Aviary.com
* Blitline não aceita carregamentos de imagem, não é possível emitir as imagens para Blitline diretamente. Tem de emiti-las para o Storage do Azure ou outros locais Blitline suporta e, em seguida, informar Blitline onde ir obtê-los.
* Blitline é paralelo em massa e não efetua qualquer processamento síncrono. O que significa deve dar-num postback_url e podem indicar-lhe quando são terminados processamento.

## <a name="create-a-blitline-account"></a>Criar uma conta de Blitline
[!INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Como criar uma tarefa de Blitline
Blitline utiliza JSON para definir as ações que pretende tirar uma imagem. Este JSON é composto por alguns campos simples.

O exemplo mais simples é o seguinte:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Aqui temos JSON que executará uma imagem de "src" "... boys.jpeg" e, em seguida, redimensione dessa imagem para 240 x 140.

O ID de aplicação é algo que pode encontrar no seu **informações de ligação** ou **GERIR** separador no Azure. É o identificador SECRETO, que permite-lhe executar tarefas em Blitline.

O parâmetro de "Guardar" identifica informações sobre onde pretende colocar a imagem assim que iremos tem ser processada. Neste caso trivial, ainda não definido um. Se não for definida nenhum localização Blitline armazenará-la localmente (e temporariamente) numa localização de nuvem exclusivo. Irá conseguir aproveitar o JSON devolvido pelo Blitline quando efetua o Blitline nessa localização. O identificador "imagem" é necessário e é devolvido para si quando identificar esta determinado guardado imagem.

Pode encontrar mais informações sobre o *funções* suportamos aqui: <http://www.blitline.com/docs/functions>

Também pode encontrar documentação sobre as opções de tarefa aqui: <http://www.blitline.com/docs/api>

Assim que tiver o seu JSON é tudo o que precisa fazer **POST** para`http://api.blitline.com/job`

Irá obter novamente JSON que procura algo semelhante ao seguinte:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Isto indica que Blitline recebeu o pedido, tem colocá-la numa fila de processamento e, quando tiver concluído a imagem estarão disponível em: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_aplicação\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Como guardar uma imagem para a sua conta do Storage do Azure
Se tiver uma conta de armazenamento do Azure, pode facilmente tiver Blitline enviar as imagens processadas para o contentor do Azure. Adicionando um "azure_destination" definir as permissões para Blitline enviar para e a localização.

Segue-se um exemplo:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Ao preencher os valores CAPITALIZED com os seus próprios, pode submeter este JSON para http://api.blitline.com/job e a imagem "src" será processada com um filtro de blur e, em seguida, enviada para o destino do Azure.

### <a name="please-note"></a>Atenção:
O SAS tem de conter o url SAS completo, incluindo o nome de ficheiro do ficheiro de destino.

Exemplo:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Também pode ler a edição mais recente do docs de armazenamento do Azure do Blitline [aqui](http://www.blitline.com/docs/azure_storage).

## <a name="next-steps"></a>Passos Seguintes
Visite blitline.com para ler mais sobre todas as nossas outras funcionalidades:

* Documentos de ponto final de API Blitline <http://www.blitline.com/docs/api>
* Funções de Blitline API <http://www.blitline.com/docs/functions>
* Exemplos de Blitline API <http://www.blitline.com/docs/examples>
* Terceira parte Nuget biblioteca <http://nuget.org/packages/Blitline.Net>

