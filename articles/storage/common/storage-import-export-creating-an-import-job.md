---
title: "Criar uma tarefa de importação para importar/exportar do Azure | Microsoft Docs"
description: "Saiba como criar uma importação para o serviço de importação/exportação do Microsoft Azure."
author: muralikk
manager: syadav
editor: syadav
services: storage
documentationcenter: 
ms.assetid: 8b886e83-6148-4149-9d0f-5d48ec822475
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: d373d2a0e601f2796719fc5efb8761f276ab24d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Criar uma tarefa de importação para o serviço do Azure para importar/exportar

Criar uma tarefa de importação para o serviço de importação/exportação do Microsoft Azure utilizando a API REST envolve os seguintes passos:

-   A preparar unidades com a ferramenta de importação/exportação do Azure.

-   Obter a localização para a qual são enviados a unidade.

-   Criar a tarefa de importação.

-   As unidades para a Microsoft através de um serviço de carrier suportado de envio.

-   Atualizar a tarefa de importação com os detalhes de envio.

 Consulte [utilizando o serviço de importação/exportação do Microsoft Azure para transferir dados para o Blob Storage](storage-import-export-service.md) para uma descrição geral do serviço de importação/exportação e um tutorial que demonstra como utilizar o [portal do Azure](https://portal.azure.com/) para criar e gerir a importar e exportar tarefas.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Preparar as unidades com a ferramenta de importação/exportação do Azure

Os passos para preparar unidades para uma tarefa de importação são os mesmos, se criar o jobvia o portal ou através da API REST.

Segue-se uma breve descrição geral da preparação da unidade. Consulte o [Azure importação ExportTool referência](storage-import-export-tool-how-to-v1.md) para obter instruções completas. Pode transferir a ferramenta de importação/exportação do Azure [aqui](http://go.microsoft.com/fwlink/?LinkID=301900).

Envolve a preparação da sua unidade:

-   Identificar os dados a ser importados.

-   Identificar os blobs de destino no armazenamento do Microsoft Azure.

-   Utilizar a ferramenta de importação/exportação do Azure para copiar os dados para um ou mais unidades de disco rígido.

 A ferramenta de importação/exportação do Azure também irá gerar um ficheiro de manifesto para cada um das unidades que está preparado. Contém um ficheiro de manifesto:

-   Enumeração de todos os ficheiros concebida para carregar e os mapeamentos destes ficheiros para os blobs.

-   Somas de verificação de segmentos de cada ficheiro.

-   Informações sobre os metadados e as propriedades para associar cada blob.

-   Obter uma listagem da ação a tomar se um blob que está a ser carregado tem o mesmo nome de um blob existente no contentor. Opções possíveis: a) substituir o blob com o ficheiro, b) manter o blob existente e ignorar carregamento do ficheiro, c) Acrescentar um sufixo no nome, para que este não entra em conflito com outros ficheiros.

## <a name="obtaining-your-shipping-location"></a>Obter a localização de envio

Antes de criar uma tarefa de importação, tem de obter um envio de localização de nome e endereço ao chamar o [lista localizações](/rest/api/storageimportexport/listlocations) operação. `List Locations`irá devolver uma lista de localizações e os respetivos endereços de correio postal. Pode selecionar uma localização da lista devolvida e são enviados os discos rígidos a esse endereço. Também pode utilizar o `Get Location` operação para obter o endereço de envio para uma localização específica diretamente.

 Siga os passos abaixo para obter a localização do envio:

-   Identifica o nome da localização da sua conta de armazenamento. Este valor pode ser encontrado no **localização** campo na conta de armazenamento **Dashboard** no Azure portal ou consultado para utilizando a operação de API de gestão do serviço [obter propriedades de conta de armazenamento](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Obter a localização que esteja disponível para processar esta conta de armazenamento ao chamar o `Get Location` operação.

-   Se o `AlternateLocations` propriedade da localização contém a localização de si próprio, em seguida, pode utilizar esta localização. Caso contrário, chame o `Get Location` operação com uma das localizações alternativas. A localização original pode ser fechada temporariamente para manutenção.

## <a name="creating-the-import-job"></a>Criar a tarefa de importação
Para criar a tarefa de importação, chame o [colocar tarefa](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operação. Terá de fornecer as seguintes informações:

-   Um nome para a tarefa.

-   O nome da conta de armazenamento.

-   O nome da localização envio, obtido a partir do passo anterior.

-   Um tipo de tarefa (importar).

-   O endereço do remetente onde as unidades devem ser enviadas uma vez concluída a tarefa de importação.

-   A lista de unidades na tarefa. Para cada unidade, tem de incluir as seguintes informações que foi obtidas durante o passo de preparação de unidade:

    -   A Id de unidade

    -   A chave do BitLocker

    -   O caminho relativo do ficheiro de manifesto no disco rígido

    -   O Base16 codificado hash de ficheiro de manifesto MD5

## <a name="shipping-your-drives"></a>As unidades de envio
Tem de enviar a unidades para o endereço que obteve no passo anterior e tem de fornecer o serviço de importação/exportação com o número de controlo do pacote.

> [!NOTE]
>  Tem de enviar a unidades através de um serviço operadora suportados, o que irá fornecer um número de controlo do seu pacote.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Atualizar a tarefa de importação com as suas informações de envio
Depois de ter o seu número de controlo, chame o [atualizar propriedades da tarefa](/api/storageimportexport/jobs#Jobs_Update) operação para atualizar o nome da operadora envio, o número de controlo para a tarefa e o número de conta operadora para envio de retorno. Opcionalmente, pode especificar o número de unidades e, bem como a data de envio.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o REST API do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
