---
title: "Azure Active Directory Domain Services: Atualizar as definições de DNS para a Azure Virtual Network | Microsoft Docs"
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
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abb27292d4b5533fe6f3d66d6921fea8c82f18dd
ms.lasthandoff: 04/12/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Atualizar as definições de DNS para a Azure Virtual Network
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4: Atualizar as definições de DNS para a Azure Virtual Network
Nas tarefas de configuração anteriores, ativou com êxito o Azure Active Directory Domain Services no seu diretório. A tarefa seguinte é garantir que os computadores na rede virtual se conseguem ligar e consumir esses serviços. Neste artigo, atualiza as definições do servidor DNS da sua rede virtual para que apontem para os dois endereços IP nos quais o Azure Active Directory Domain Services está disponível na rede virtual.

> [!NOTE]
> Depois de ter ativado o Azure Active Directory Domain Services para o diretório, tome nota dos endereços IP para o Azure Active Directory Domain Services que são apresentados no separador **Configurar** do seu diretório.
>
>

Para atualizar a definição do servidor DNS para a rede virtual em que tiver ativado o Azure Active Directory Domain Services, faça o seguinte:

1. Aceda ao [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel da esquerda, selecione **Redes**.  
    A janela **Redes** abre.

    ![Janela de Redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. No separador **Redes virtuais**, selecione a rede virtual em que ativou o Azure Active Directory Domain Services para ver as respetivas propriedades.
4. Clique no separador **Configurar**.

    ![Janela de Redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. Na secção dos **servidores DNS**, introduz os endereços IP que foram apresentados na secção **Serviços de Domínio** no separador **Configurar** do seu diretório.
6. Para guardar as definições do servidor DNS desta rede virtual, no painel da tarefa, na parte inferior da página clique em **Guardar**.

   ![Atualize as definições do servidor DNS para a rede virtual](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Depois de atualizar as definições do servidor DNS para a rede virtual, poderá demorar algum tempo para as máquinas virtuais na rede obterem a configuração de DNS atualizada. Se uma máquina virtual não consegue ligar ao domínio, pode esvaziar a cache DNS ("ipconfig/flushdns") na máquina virtual. Este comando força uma atualização das definições de DNS na máquina virtual.
>
>

## <a name="next-steps"></a>Passos seguintes
Tarefa 5: [Ativar a sincronização de palavras-passe para o Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

