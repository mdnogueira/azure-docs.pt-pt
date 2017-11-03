---
title: "Atualize os serviços de suporte de dados depois de implementar chaves de acesso de armazenamento | Microsoft Docs"
description: "Este artigo dão-lhe orientações sobre como atualizar serviços de suporte de dados depois de implementar chaves de acesso de armazenamento."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: 304e72e0d2d4a7e95df513e6d5481def9eae3f68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Atualize os serviços de suporte de dados depois de implementar chaves de acesso de armazenamento

Quando cria uma nova conta de serviços de suporte de dados do Azure (AMS), é-lhe também pedido para selecionar uma conta de armazenamento do Azure que é utilizada para armazenar o conteúdo do suporte de dados. Pode adicionar mais contas de armazenamento à sua conta de Media Services. Este tópico mostra como Rodar chaves de armazenamento. Também mostra como adicionar contas de armazenamento para uma conta de suporte de dados. 

Para executar as ações descritas neste tópico, o que deve utilizar [ARM APIs](https://docs.microsoft.com/rest/api/media/mediaservice) e [Powershell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  Para obter mais informações, consulte [como gerir recursos do Azure com o PowerShell e do Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).

## <a name="overview"></a>Descrição geral

Quando é criada uma nova conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são utilizadas para autenticar o acesso à sua conta de armazenamento. Para manter as ligações de armazenamento mais segura, é recomendado para periodicamente regenerar e roda para a sua chave de acesso de armazenamento. Duas chaves de acesso (principais e secundários) são fornecidas para ativar a manter as ligações à conta de armazenamento com uma chave de acesso enquanto volta a gerar a chave de acesso. Este procedimento também é denominado "chaves de acesso graduais".

Serviços de suporte de dados depende de uma chave de armazenamento fornecida ao mesmo. Especificamente, os localizadores que são utilizados para transmitir ou transferir os seus recursos dependem da chave de acesso de armazenamento especificada. Quando é criada uma conta de AMS demora uma dependência na chave de acesso de armazenamento primário por predefinição, mas como um utilizador pode atualizar a chave de armazenamento que tenha de AMS. Deve certificar-se permitir que os Media Services saber qual a chave a utilizar ao seguir os passos descritos neste tópico.  

>[!NOTE]
> Se tiver várias contas de armazenamento, deverá efetuar este procedimento com cada conta de armazenamento. A ordem na qual Rodar chaves de armazenamento não estiver corrigida. Pode rodar o primeiro chave secundário e, em seguida, a ASR primário de chave ou vice-versa.
>
> Antes de executar os passos descritos neste tópico, uma conta de produção, certifique-se testar uma conta de pré-produção.
>

## <a name="steps-to-rotate-storage-keys"></a>Passos para rodar chaves de armazenamento 
 
 1. Alterar a chave primária de conta de armazenamento através do cmdlet do powershell ou [Azure](https://portal.azure.com/) portal.
 2. Chame o cmdlet de sincronização AzureRmMediaServiceStorageKeys com os parâmetros adequados para forçar a conta de media para recolher chaves de conta de armazenamento
 
    O exemplo seguinte mostra como sincronizar chaves para contas de armazenamento.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Aguarde aproximadamente, uma hora. Certifique-se de que os cenários de transmissão em fluxo estão a funcionar.
 4. Alterar a chave secundária com uma conta de armazenamento através do cmdlet do powershell ou o portal do Azure.
 5. Chame sincronização AzureRmMediaServiceStorageKeys powershell com os parâmetros adequados para forçar a conta de media para recolher chaves de conta de armazenamento novas. 
 6. Aguarde aproximadamente, uma hora. Certifique-se de que os cenários de transmissão em fluxo estão a funcionar.
 
### <a name="a-powershell-cmdlet-example"></a>Um exemplo de cmdlet do powershell 

O exemplo seguinte demonstra como obter a conta de armazenamento e sincronizá-lo com a conta de AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Passos para adicionar contas de armazenamento à sua conta de AMS

O tópico seguinte mostra como adicionar contas de armazenamento à sua conta de AMS: [ligar várias contas de armazenamento para uma conta de Media Services](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Em que as confirmações
Gostaríamos de reconhecer seguintes pessoas que contribuíram para criar este documento: Cenk Dingiloglu, Milan Gada, Seva Titov.
