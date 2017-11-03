---
title: Utilizar o portal de administrador na pilha do Azure | Microsoft Docs
description: Como um operador de pilha do Azure, saiba como utilizar o portal de administrador.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3a1be7a08fab8ad0253f26e6a0683617bff4b7c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Utilizar o portal de administrador na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Existem dois portais na pilha do Azure; o portal de administrador e o portal de utilizador (por vezes referido como o *inquilino* portal). Como um operador de pilha do Azure, pode utilizar o portal de administrador para gestão diária e as operações da pilha do Azure. 

## <a name="access-the-administrator-portal"></a>Aceder ao portal de administrador

Para um ambiente do kit de desenvolvimento, tem de primeiro certifique-se de que pode [ligar para o anfitrião do kit de desenvolvimento](azure-stack-connect-azure-stack.md) através de ligações de ambiente de trabalho remoto ou através de uma rede privada virtual (VPN).

Para aceder ao portal de administrador, navegue para o URL e o início de sessão utilizando as credenciais de um operador de pilha do Azure portal. Para um sistema integrado, portal que URL varia com base no nome de região e externo nome de domínio completamente qualificado (FQDN) da sua implementação de pilha do Azure.

| Ambiente | URL do Portal de administrador |   
| -- | -- | 
| Kit de desenvolvimento| https://adminportal.local.azurestack.external  |
| Sistemas integrados | https://adminportal. &lt; *região*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![O portal de administrador](media/azure-stack-manage-portals/image1.png)

No portal do administrador, pode fazer coisas como:

* Gerir a infraestrutura (incluindo o estado de funcionamento do sistema, atualizações, capacidade, etc.)
* Preencher o marketplace
* criar planos e ofertas
* criar subscrições para os utilizadores

No **tutorial de início rápido** mosaico, existem ligações para documentação online para as tarefas mais comuns.
 
Embora a capacidade de um operador criar recursos, tais como máquinas virtuais, redes virtuais e contas de armazenamento no portal do administrador, deve [inicie sessão no portal de utilizador](user/azure-stack-use-portal.md) para criar e testar recursos. (O **criar uma máquina virtual** ligação no mosaico do tutorial de início rápido tem a criar uma máquina virtual no portal do administrador, mas este procedimento é apenas para validar a pilha do Azure após a implementação inicial.)

## <a name="subscription-behavior"></a>Comportamento de subscrição
 
Não há apenas uma subscrição que está disponível no portal do administrador. Esta subscrição está a *subscrição do fornecedor predefinido*. Não é possível adicionar quaisquer outras subscrições para utilização no portal do administrador.

Como um operador de pilha do Azure, pode adicionar subscrições para os seus utilizadores (incluindo por si) a partir do portal do administrador. Os utilizadores (incluindo por si) podem aceder e utilizar estas subscrições no portal de utilizador. O portal de utilizador não fornecer acesso a qualquer uma das funcionalidades administrativas ou operacionais do portal do administrador.

Os portais de administrador e utilizador são apoiados por instâncias separadas do Gestor de recursos do Azure. Devido a separação do Resource Manager, subscrições não cruzada portais. Por exemplo, se o utilizador, como um operador de pilha do Azure inicia sessão no portal de utilizador, não é possível aceder a subscrição do fornecedor predefinido. Por conseguinte, não tem acesso a quaisquer funções administrativas. Pode criar subscrições para si próprio de ofertas públicas, mas são consideradas um utilizador de inquilino.

  >[!NOTE]
  No ambiente do kit de desenvolvimento, se um utilizador pertencer ao mesmo diretório do inquilino o operador de pilha do Azure, estes não estão bloqueadas de iniciar sessão portal do administrador. No entanto, não vão conseguir aceder qualquer uma das funções administrativas. Além disso, no portal de administrador, eles não é possível adicionar subscrições ou acesso oferece que são disponibilizadas no portal de utilizador.

## <a name="administrator-portal-tips"></a>Sugestões de portais de administrador

### <a name="customize-the-dashboard"></a>Personalizar o dashboard

O dashboard contém um conjunto de mosaicos predefinidos. Pode clicar em **editar dashboard** para modificar o dashboard predefinido ou clique em **novo dashboard** adicionar dashboards personalizados. Pode facilmente adicionar mosaicos ao dashboard. Por exemplo, pode clicar em **novo**, faça duplo clique **oferece + planos**e, em seguida, clique em **afixar ao dashboard**.

### <a name="quick-access-to-online-documentation"></a>Acesso rápido a documentação online

Para aceder à documentação do operador de pilha do Azure, clique no ícone de ajuda e suporte (ponto de interrogação) no canto superior direito do portal de administrador e, em seguida, clique em **ajuda + suporte**.

### <a name="quick-access-to-help-and-support"></a>Acesso rápido a ajuda e suporte

Se clicar no ícone de ajuda e suporte (ponto de interrogação) no canto superior direito do portal de administrador e, em seguida, clique em **novo pedido de suporte**, isto faz o seguinte:

- Se estiver a utilizar um sistema integrado, esta ação abre-se um site, onde pode diretamente abrir um pedido de suporte com o Microsoft ao cliente suportar (CSS). Consulte a secção "Onde obter suporte" [Noções básicas de administração do Azure pilha](azure-stack-manage-basics.md) compreender quando deve passar através de suporte da Microsoft ou através do suporte de fornecedor de hardware de fabricante de equipamento original (OEM).
- Se estiver a utilizar o kit de desenvolvimento, esta ação abre-se o site de fóruns do Azure pilha diretamente. Estes fóruns regularmente são monitorizados. Porque o kit de desenvolvimento é um ambiente de avaliação, não há nenhum oficial de suporte oferecida através do Microsoft CSS.

## <a name="next-steps"></a>Passos seguintes

- [Gestão de região na pilha do Azure](azure-stack-region-management.md)
