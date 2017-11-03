---
title: "Criar uma tarefa para Azure importação/exportação de exportação | Microsoft Docs"
description: "Saiba como criar uma tarefa de exportação para o serviço de importação/exportação do Microsoft Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: bdeac373aa8270bd9de8f135ec7166d744fd83ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Criar uma tarefa de exportação para o serviço do Azure para importar/exportar
Criar uma tarefa de exportação para o serviço de importação/exportação do Microsoft Azure utilizando a API REST envolve os seguintes passos:

-   Selecionar os blobs para exportar.

-   Obter uma localização de envio.

-   Criar a tarefa de exportação.

-   Envio a unidades vazia para a Microsoft através de um serviço de carrier suportados.

-   Atualizar a tarefa de exportação com a informação do pacote.

-   Receber as unidades de volta da Microsoft.

 Consulte [utilizando o serviço de importação/exportação do Windows Azure para transferir dados para o Blob Storage](storage-import-export-service.md) para uma descrição geral do serviço de importação/exportação e um tutorial que demonstra como utilizar o [portal do Azure](https://portal.azure.com/) para criar e gerir a importar e exportar tarefas.

## <a name="selecting-blobs-to-export"></a>A seleção de blobs para exportar
 Para criar uma tarefa de exportação, terá de fornecer uma lista de blobs que pretende exportar da sua conta de armazenamento. Existem algumas formas para selecionar os blobs para ser exportada:

-   Pode utilizar um caminho relativo de BLOBs para selecionar um único blob e todos os respetivos instantâneos.

-   Pode utilizar um caminho relativo de BLOBs para selecionar um único blob excluindo respetivos instantâneos.

-   Pode utilizar um caminho relativo de blob e um tempo de instantâneos para selecionar um único instantâneo.

-   Pode utilizar um prefixo de BLOBs para selecionar todos os blobs e instantâneos com o prefixo especificado.

-   Pode exportar todos os blobs e instantâneos na conta de armazenamento.

 Para obter mais informações sobre como especificar blobs para exportar, consulte o [colocar tarefa](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operação.

## <a name="obtaining-your-shipping-location"></a>Obter a localização de envio
Antes de criar uma tarefa de exportação, tem de obter um envio de localização de nome e endereço ao chamar o [obter localização](https://portal.azure.com) ou [lista localizações](/rest/api/storageimportexport/listlocations) operação. `List Locations`irá devolver uma lista de localizações e os respetivos endereços de correio postal. Pode selecionar uma localização da lista devolvida e são enviados os discos rígidos a esse endereço. Também pode utilizar o `Get Location` operação para obter o endereço de envio para uma localização específica diretamente.

Siga os passos abaixo para obter a localização do envio:

-   Identifica o nome da localização da sua conta de armazenamento. Este valor pode ser encontrado no **localização** campo na conta de armazenamento **Dashboard** no clássica portal ou consultada para utilizando a operação de API de gestão do serviço [obter propriedades de conta de armazenamento](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Obter a localização que estão disponíveis para processar esta conta de armazenamento ao chamar o `Get Location` operação.

-   Se o `AlternateLocations` propriedade da localização contém a localização de si próprio, em seguida, pode utilizar esta localização. Caso contrário, chame o `Get Location` operação com uma das localizações alternativas. A localização original pode ser fechada temporariamente para manutenção.

## <a name="creating-the-export-job"></a>Criar a tarefa de exportação
 Para criar a tarefa de exportação, chame o [colocar tarefa](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operação. Terá de fornecer as seguintes informações:

-   Um nome para a tarefa.

-   O nome da conta de armazenamento.

-   O nome da localização envio, obtido no passo anterior.

-   Um tipo de tarefa (exportação).

-   O endereço do remetente onde as unidades devem ser enviadas uma vez concluída a tarefa de exportação.

-   A lista de blobs (ou prefixos de blob) para ser exportada.

## <a name="shipping-your-drives"></a>As unidades de envio
 Em seguida, utilize a ferramenta de importação/exportação do Azure para determinar o número de unidades, que terá de enviar, com base em blobs que selecionou para ser exportada e o tamanho de unidade. Consulte o [referência de ferramenta de importação/exportação do Azure](storage-import-export-tool-how-to-v1.md) para obter mais detalhes.

 As unidades num único pacote do pacote e envie-os para o endereço obtido no passo anterior. Tenha em atenção o número de controlo do pacote para o passo seguinte.

> [!NOTE]
>  Tem de enviar a unidades através de um serviço operadora suportados, o que irá fornecer um número de controlo do seu pacote.

## <a name="updating-the-export-job-with-your-package-information"></a>Atualizar a tarefa de exportação com as suas informações de pacote
 Depois de ter o seu número de controlo, chame o [propriedades da tarefa de atualização](/rest/api/storageimportexport/jobs#Jobs_Update) operação para atualizar o nome da operadora e número para a tarefa de controlo. Opcionalmente, pode especificar o número de unidades, o endereço do remetente e, bem como a data de envio.

## <a name="receiving-the-package"></a>Receber o pacote
 Depois de ter sido processada a tarefa de exportação, as unidades serão devolvidas, com os seus dados encriptados. Pode obter a chave do BitLocker para cada unidades ao chamar o [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operação. Em seguida, pode desbloquear a unidade com a chave. O ficheiro de manifesto de unidade em cada unidade contém a lista de ficheiros na unidade, bem como o endereço de blob original para cada ficheiro.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o REST API do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
