---
title: "Azure Active Directory dos serviços de domínio: Ativar o suporte para o serviço de perfil de utilizador do SharePoint | Microsoft Docs"
description: "Configurar o Azure Active Directory Domain Services domínios geridos para suportar a sincronização de perfil para o SharePoint Server"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: c3c923b5c9cd652f0c5b0ec98c1cda740f180122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Configurar um domínio gerido para suportar a sincronização de perfil para o SharePoint Server
Servidor do SharePoint inclui um serviço de perfil de utilizador que é utilizada para sincronização de perfil de utilizador. Para configurar o serviço de perfil de utilizador, as permissões adequadas tem de ser concedido um domínio do Active Directory. Para obter mais informações, consulte [conceder permissões de serviços de domínio do Active Directory para a sincronização de perfil no SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

Este artigo explica como pode configurar os serviços de domínio do Azure AD domínios geridos para implementar o serviço de sincronização de perfil de utilizador do SharePoint Server.

## <a name="the-aad-dc-service-accounts-group"></a>O grupo 'Contas de serviço do AAD DC'
Um grupo de segurança chamado '**contas de serviço do AAD DC**' está disponível na unidade organizacional 'Utilizadores' no seu domínio gerido. Pode ver este grupo no **computadores e utilizadores do Active Directory** snap-in MMC no seu domínio gerido.

![Grupo de segurança de contas de serviço de DC do AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Os membros deste grupo de segurança são delegados os seguintes privilégios:
- O privilégio 'Replicar alterações de diretório' na raiz da DSE de domínio gerido.
- O privilégio 'Replicar alterações de diretório' no contexto de nomenclatura de configuração (cn = contentor de configuração) do domínio gerido.

Este grupo de segurança também é um membro do grupo incorporado **acesso de compatibilidade anterior ao Windows 2000**.

![Grupo de segurança de contas de serviço de DC do AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Ativar o seu domínio gerido suportar a sincronização de perfil de utilizador do servidor do SharePoint
Pode adicionar a conta de serviço utilizada para sincronização de perfil de utilizador de SharePoint para o **contas de serviço do AAD DC** grupo. Como resultado, a conta de sincronização obtém privilégios adequados para replicar as alterações para o diretório. Este passo de configuração ativa a sincronização de perfil de utilizador de servidor do SharePoint para funcionar corretamente.

![As contas de serviço do AAD DC - adicionar membros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![As contas de serviço do AAD DC - adicionar membros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Conteúdo relacionado
* [Referência técnica - serviços de domínio do Active Directory conceder as permissões para sincronização do perfil no SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
