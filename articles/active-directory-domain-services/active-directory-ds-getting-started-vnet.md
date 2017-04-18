---
title: 'Azure Active Directory Domain Services: Criar ou selecionar uma rede virtual | Microsoft Docs'
description: "Introdução aos Serviços de Domínio do Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: cb372232492e8f98ff1543798b92b4b60fc25021
ms.lasthandoff: 04/12/2017


---
# <a name="create-or-select-a-virtual-network-for-azure-active-directory-domain-services"></a>Criar ou selecionar uma rede virtual para o Azure Active Directory Domain Services
## <a name="before-you-begin"></a>Antes de começar
Consulte [Networking considerations for Azure Active Directory Domain Services (Considerações sobre o funcionamento em rede do Azure Active Directory Domain Services)](active-directory-ds-networking.md).

## <a name="task-2-create-an-azure-virtual-network"></a>Tarefa 2: Criar uma Azure Virtual Network
A tarefa de configuração seguinte é criar uma rede virtual do Azure e uma sub-rede dentro da mesma. O Azure Active Directory Domain Services é ativado nesta sub-rede dentro da sua rede virtual. Se tiver uma rede virtual existente que prefira utilizar, pode ignorar este passo.

> [!NOTE]
> Certifique-se de que a Azure Virtual Network que cria ou opta por utilizar com o Azure Active Directory Domain Services pertence a uma região do Azure que seja suportado pelo Azure Active Directory Domain Services. Para verificar em que regiões do Azure está disponível o Azure Active Directory Domain Services, consulte [Azure services by region (Serviços do Azure por região)](https://azure.microsoft.com/regions/#services/).
>
>Anote o nome da rede virtual para se certificar que selecionou a rede virtual correta quando ativar o Azure Active Directory Domain Services num passo subsequente da configuração.


Para criar uma rede virtual do Azure na qual pretende ativar o Azure Active Directory Domain Services, siga estas instruções de configuração:

1. Aceda ao [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel da esquerda, selecione **Redes**.

    ![Nó de redes](./media/active-directory-domain-services-getting-started/networks-node.png)  
    A janela **Redes Virtuais** abre.
3. No painel de tarefas na parte inferior da janela, clique em **Novo**.

    ![Janela de Redes Virtuais](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Clique em **Serviços de Rede** e selecione **Rede Virtual**.
    
    ![Rede virtual – criação rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. Para criar uma rede virtual, clique em **Criação Rápida**.
    
6. Especifique um **Nome** para a sua rede virtual e considere efetuar o seguinte: 
    * Pode optar por configurar o **Espaço de endereço** ou o **Número máximo de VMs** para esta rede. 
    * Pode deixar a definição do **servidor DNS** como **Nenhum** por agora. Pode atualizar a definição depois de ativar o Azure Active Directory Domain Services.
7. Na lista pendente **Localização**, selecione uma região do Azure suportada.  
    Para verificar em que regiões do Azure está disponível o Azure Active Directory Domain Services, consulte [Azure services by region (Serviços do Azure por região)](https://azure.microsoft.com/regions/#services/).
8. Para criar a rede virtual, clique em **Criar uma Rede Virtual**.

    ![Criar uma rede virtual para o Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. Depois de criar uma rede virtual, selecione o nome da mesma e clique no separador **Configurar**.

    ![Criar uma sub-rede](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Em **espaços de endereços de rede virtual**, clique em **adicionar sub-rede** e, em seguida, especifique uma sub-rede com o nome **AaddsSubnet**. 

    ![Criar uma sub-rede para o Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. Para criar a sub-rede, clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes
Tarefa 3: [Ativar o Azure Active Directory Domain Services](active-directory-ds-getting-started-enableaadds.md)

