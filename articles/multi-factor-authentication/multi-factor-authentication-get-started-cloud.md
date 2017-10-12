---
title: "Introdução ao MFA do Azure na nuvem | Microsoft Docs"
description: "Esta é a página do Multi-Factor Authentication do Microsoft Azure que descreve como iniciar o MFA do Azure na nuvem."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: kgremban
ms.openlocfilehash: 19f3228b874fc4e37bf83388dae4341428226482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introdução ao Multi-Factor Authentication do Azure na nuvem
Este artigo descreve como começar a utilizar o Multi-Factor Authentication do Azure na nuvem.

> [!NOTE]
> A seguinte documentação fornece informações sobre como ativar utilizadores através do **Portal Clássico do Azure**. Se estiver à procura de informações sobre como configurar o Multi-Factor Authentication do Azure para utilizadores do O365, veja [Configurar a autenticação multifator para o Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US).

![MFA na Nuvem](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Pré-requisito
[Inscreva-se numa subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/) - Se ainda não possui uma subscrição do Azure, tem de se inscrever numa. Se estiver apenas a começar e a utilizar o MFA do Azure, pode utilizar uma subscrição de avaliação

## <a name="enable-azure-multi-factor-authentication"></a>Ativar a Multi-Factor Authentication do Azure
Desde que os utilizadores tenham licenças que incluam a Multi-Factor Authentication do Azure, não é necessário fazer nada para ativar a MFA do Azure. Pode requerer a verificação de dois passos para cada utilizador. As licenças que permitem a MFA do Azure são:
- Multi-Factor Authentication do Azure
- Azure Active Directory Premium
- Enterprise Mobility + Security

Se não tiver uma destas três licenças ou não tiver suficiente licenças para abranger todos os utilizadores, não há problema. Apenas tem de efetuar um passo extra e [Criar um fornecedor de Multi-Factor Auth](multi-factor-authentication-get-started-auth-provider.md) no diretório.

## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação de dois passos para os utilizadores

Utilize um dos procedimentos listados em [Como requerer a verificação de dois passos para um utilizador ou grupo](multi-factor-authentication-get-started-user-states.md) para começar a utilizar o MFA do Azure. Pode optar por impor a verificação de dois passos para todos os inícios de sessão ou pode criar políticas de acesso condicional para exigir a verificação apenas quando lhe interessa.

## <a name="next-steps"></a>Passos Seguintes
Agora que configurou o Multi-Factor Authentication do Azure na nuvem, pode configurar e definir a sua implementação. Veja [Configuring Azure Multi-Factor Authentication (Configurar o Multi-Factor Authentication do Azure)](multi-factor-authentication-whats-next.md) para obter mais detalhes.

