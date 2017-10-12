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
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: ab8e3215e8e73d3943af06cffafa730cf1b7744b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services"></a>Ativar o Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4: Atualizar as definições de DNS para a rede virtual do Azure
Nas tarefas de configuração anteriores, ativou com êxito o Azure Active Directory Domain Services no seu diretório. A tarefa seguinte é garantir que os computadores na rede virtual se conseguem ligar e consumir esses serviços. Neste artigo, atualiza as definições do servidor DNS da sua rede virtual para que apontem para os dois endereços IP nos quais o Azure Active Directory Domain Services está disponível na rede virtual.

Para atualizar a definição do servidor DNS para a rede virtual em que tenha ativado o Azure Active Directory Domain Services, faça o seguinte:

1. O separador **Descrição geral** lista um conjunto de **Passos de configuração necessários** para serem executados depois dee o domínio gerido estar totalmente aprovisionado. O primeiro passo de configuração é **Definições do servidor DNS para a sua rede virtual**.

    ![Serviços de domínio - separador Descrição geral depois de totalmente aprovisionado](./media/getting-started/domain-services-provisioned-overview.png)

2. Quando o seu domínio está totalmente aprovisionado, dois endereços IP são exibidos neste mosaico. Cada um desses endereços IP representa um controlador de domínio para o seu domínio gerido.

3. Para copiar o primeiro endereço IP para a área de transferência, clique no botão Copiar junto ao mesmo. Em seguida, clique no botão **Configurar servidores DNS**.

4. Cole o primeiro endereço IP na caixa de texto **Adicionar servidor DNS** no painel **Servidores DNS**. Desloque-se horizontalmente para a esquerda para copiar o segundo endereço IP e cole-o na caixa de texto **Adicionar servidor DNS**.

    ![Serviços de domínio - atualizar o DNS](./media/getting-started/domain-services-update-dns.png)

5. Clique em **Guardar** quando terminar de atualizar os servidores DNS para a rede virtual.

> [!NOTE]
> As máquinas virtuais na rede só obtêm as novas definições de DNS após uma reinicialização. Se precisar de atualizar imediatamente as definições do DNS, ative a reinicialização no portal, no PowerShell ou na CLI.
>
>

## <a name="next-step"></a>Passo seguinte
[Tarefa 5: Ativar a sincronização de palavras-passe para o Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
