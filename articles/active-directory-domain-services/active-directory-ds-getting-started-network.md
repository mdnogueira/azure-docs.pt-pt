---
title: "Serviços de domínio do Azure Active Directory: Introdução | Microsoft Docs"
description: Ativar o Azure Active Directory Domain Services no portal do Azure
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: a476e5228fc7fb83c1fe0b7ae0989a7752be09e1
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Ativar o Azure Active Directory Domain Services no portal do Azure


## <a name="before-you-begin"></a>Antes de começar
Consulte [Networking considerations for Azure Active Directory Domain Services (Considerações sobre o funcionamento em rede do Azure Active Directory Domain Services)](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Tarefa 2: Configurar definições de rede
A próxima tarefa de configuração é criar uma Azure virtual network e uma sub-rede dedicada dentro da mesma. O Azure Active Directory Domain Services é ativado nesta sub-rede dentro da sua rede virtual. Também pode escolher uma rede virtual existente e criar a sub-rede dedicada dentro da mesma.

1. Clique em **rede Virtual** para selecionar uma rede virtual.
    > [!NOTE]
    > **Redes virtuais clássicas não são suportadas para novas implementações.** Redes virtuais clássicas não são suportadas para novas implementações. Domínios geridos existentes implementados em redes virtuais clássicas continuam a ser suportada. Podemos irá fornecer a capacidade de migrar um domínio gerido existente de uma rede virtual clássica a uma rede virtual do Gestor de recursos num futuro próximo.
    >

2. No **escolha de rede virtual** página, pode ver todas as redes virtuais existentes. Verá apenas as redes virtuais que pertencem ao grupo de recursos e localização do Azure que selecionou no **Noções básicas** página do assistente.
3. Escolha a rede virtual em que os serviços de domínio do Azure AD devem ser ativados. Pode escolher uma rede virtual existente ou crie um novo.

  > [!TIP]
  > **Não é possível mover o seu domínio gerido a uma rede virtual diferente depois de ativar os serviços de domínio do Azure AD.** Escolha a rede virtual correta para ativar o seu domínio gerido. Depois de criar um domínio gerido, não é possível movê-la para uma rede virtual diferente, sem eliminar o domínio gerido. Recomendamos que reveja o [redes considerações para o Azure Active Directory Domain Services](active-directory-ds-networking.md) antes de continuar.  
  >

4. **Criar rede virtual:** clique **criar nova** para criar uma nova rede virtual. Recomendamos vivamente a utilização de uma sub-rede dedicada para os serviços de domínio do Azure AD. Por exemplo, crie uma sub-rede com o nome 'DomainServices', tornando mais fácil para os outros administradores compreender o que é implementado dentro da sub-rede. Clique em **OK** quando tiver terminado.

    ![Escolha a rede virtual](./media/getting-started/domain-services-blade-network-pick-vnet.png)

5. **Rede virtual existente:** se planear escolher uma rede virtual existente, [criar uma sub-rede dedicada através da extensão de redes virtuais](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)e, em seguida, escolha dessa sub-rede. Clique em **rede Virtual** para selecionar a rede virtual existente. Clique em **sub-rede** para escolher a sub-rede dedicada na sua rede virtual existente, no qual pretende ativar o novo domínio gerido. Clique em **OK** quando tiver terminado.

    ![Escolha a sub-rede na rede virtual](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Diretrizes para selecionar uma sub-rede**
  > 1. Utilize uma sub-rede dedicada para os serviços de domínio do Azure AD. Não implemente quaisquer outras máquinas virtuais para esta sub-rede. Esta configuração permite-lhe configurar grupos de segurança de rede (NSGs) para as suas máquinas virtuais/cargas de trabalho sem perturbar o seu domínio gerido. Para obter mais informações, consulte [redes considerações para o Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Não selecione a sub-rede do Gateway para a implementação de serviços de domínio do Azure AD, porque não é uma configuração suportada.
  3. Certifique-se de que a sub-rede que selecionou tem suficiente espaço de endereço disponível - endereços IP disponíveis, pelo menos, 3 a 5.
  >

6. Quando tiver terminado, clique em **OK** prossiga para o **grupo Administrador** página do assistente.


## <a name="next-step"></a>Passo seguinte
[Tarefa 3: configurar o grupo administrativo e ativar os serviços de domínio do Azure AD](active-directory-ds-getting-started-admingroup.md)
