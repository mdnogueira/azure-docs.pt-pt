---
title: Como configurar MSI numa VM do Azure no portal do Azure
description: "Passo pelo passo as instruções para configurar uma identidade de serviço geridas (MSI) na VM do Azure, utilizando o portal do Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: bryanla
ms.openlocfilehash: 169417530da21e0c8c58cbf770fd1d26660387f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configurar uma VM geridos serviço de identidade (MSI) no portal do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identidade de serviço geridas fornece serviços do Azure com uma identidade gerido automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter de credenciais no seu código. 

Neste artigo, irá aprender como ativar e remover MSI para uma VM do Azure, utilizando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Ativar MSI durante a criação de uma VM do Azure

A partir do momento desta redação, permitir MSI durante a criação de uma VM no portal do Azure não é suportada. Em vez disso, consulte um dos seguintes artigos de início rápido de criação de VM para criar primeiro uma VM:

- [Criar uma máquina virtual do Windows com o portal do Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual Linux com o portal do Azure](../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Em seguida, avance para a secção seguinte para obter detalhes sobre como ativar o MSI da VM.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Ativar MSI numa VM do Azure existente

Se tiver uma VM que foi originalmente aprovisionada sem um MSI:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure que contém a VM. Certifique-se a sua conta pertencer a uma função que dá-lhe também permissões de escrita na VM, tais como "Contribuinte de Máquina Virtual".

2. Navegue para a Máquina Virtual pretendida.

2. A página "Configuration", ative o MSI da VM ao selecionar "Sim" em "Identidade de serviço gerida", clique em **guardar**. Esta operação pode demorar 60 segundos ou mais para concluir:

   ![Captura de ecrã de página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Remover MSI a partir de uma VM do Azure

Se tiver uma Máquina Virtual que já não necessita de um MSI:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure que contém a VM. Certifique-se a sua conta pertencer a uma função que dá-lhe também permissões de escrita na VM, tais como "Contribuinte de Máquina Virtual".

2. Navegue para a Máquina Virtual pretendida.

3. A página "Configuration", remova o MSI da VM, selecionando "Não" em "Identidade de serviço gerida" clique depois em **guardar**. Esta operação pode demorar 60 segundos ou mais para concluir:

   ![Captura de ecrã de página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](msi-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Através do portal do Azure, atribua o MSI da VM do Azure [acesso a outro recurso do Azure](msi-howto-assign-access-portal.md).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.
