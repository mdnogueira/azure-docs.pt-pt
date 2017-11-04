---
title: "Utilize o CLI 2.0 para criar uma aplicação do Azure AD e configurá-lo para aceder à API de serviços de suporte de dados do Azure | Microsoft Docs"
description: "Este tópico mostra como utilizar a CLI 2.0 para criar uma aplicação do Azure AD e configurá-lo para aceder à API de serviços de suporte de dados do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 01a2bb6d99776feec936315bc882c3097ce832d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Utilize o CLI 2.0 para criar uma aplicação AAD e configurá-lo para aceder à API de serviços de suporte de dados do Azure

Este tópico mostra como utilizar a CLI 2.0 para criar uma aplicação do Azure Active Directory (Azure AD) e o principal de serviço para aceder a recursos de Media Services do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta de Media Services do Azure no portal do Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Utilizar a Shell de nuvem do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Inicie a Shell de nuvem a partir do painel de navegação superior do portal.

    ![Cloud Shell,](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Para obter mais informações, consulte [descrição geral do Azure na nuvem Shell](../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>Criar uma aplicação do Azure AD e configurar o acesso à conta de suporte de dados com o CLI 2.0
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create -- assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Por exemplo:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Neste exemplo, o **âmbito** é o caminho de recurso completo para o suporte de dados de conta dos serviços. No entanto, o **âmbito** pode ser qualquer nível.

Por exemplo, poderia ser um dos seguintes níveis:
 
* O **subscrição** nível.
* O **grupo de recursos** nível.
* O **recursos** nível (por exemplo, uma conta dos Media).

Para obter mais informações, consulte [criar um Azure principal de serviço com o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Consulte também [Manage Role-Based o controlo de acesso com a interface de linha de comandos do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md). 

## <a name="next-steps"></a>Passos seguintes

Introdução ao [carregar ficheiros para a conta](media-services-portal-upload-files.md).
