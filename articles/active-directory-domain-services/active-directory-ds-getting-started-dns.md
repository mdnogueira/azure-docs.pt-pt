---
title: "Serviços de domínio do Azure AD: Atualizar as definições de DNS para a Azure Virtual Network | Microsoft Docs"
description: "Introdução aos Serviços de Domínio do Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 16a0019990d4839a15acb4dcac747147a6d55e1d
ms.lasthandoff: 03/07/2017


---
# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Azure AD Domain Services - atualizar as definições de DNS para a rede virtual do Azure
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4: Atualizar as definições de DNS para a Azure Virtual Network
Nas tarefas de configuração anteriores, ativou com êxito o Azure AD Domain Services no seu diretório. A tarefa seguinte é garantir que os computadores na rede virtual se conseguem ligar e consumir esses serviços. Atualize as definições do servidor DNS da sua rede virtual para que apontem para os dois endereços IP nos quais o Azure AD Domain Services está disponível na rede virtual.

> [!NOTE]
> Tome nota dos endereços IP para os Serviços de Domínio do Azure AD apresentados no separador **Configurar** do seu diretório, depois de ter ativado os Serviços de Domínio do Azure AD para o diretório.
>
>

Execute os seguintes passos de configuração para atualizar a definição do servidor DNS para a rede virtual em que tiver ativado o Azure AD Domain Services.

1. Navegue para o **Portal Clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selecione o nó **Redes** no painel da esquerda.

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. No separador **Redes virtuais**, selecione a rede virtual em que ativou os Serviços de Domínio do Azure AD para ver as respetivas propriedades.
4. Clique no separador **Configurar**.

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. Na secção **Servidores DNS**, introduza os endereços IP dos Serviços de Domínio do Azure AD.
6. Certifique-se de que introduz os endereços IP que foram apresentados na secção **Serviços de Domínio** no separador **Configurar** do seu diretório.
7. Para guardar as definições do servidor DNS desta rede virtual, clique em **Guardar** no painel da tarefa, na parte inferior da página.

   ![Atualize as definições do servidor DNS para a rede virtual.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Depois de atualizar as definições do servidor DNS para a rede virtual, poderá demorar algum tempo para as máquinas virtuais na rede obterem a configuração de DNS atualizada. Se uma máquina virtual não consegue ligar ao domínio, pode esvaziar a cache DNS (ex. “ipconfig /flushdns”) na mesma. Este comando força uma atualização das definições de DNS na máquina virtual.
>
>

## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Tarefa 5 – Ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD
A próxima tarefa de configuração é [ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).

