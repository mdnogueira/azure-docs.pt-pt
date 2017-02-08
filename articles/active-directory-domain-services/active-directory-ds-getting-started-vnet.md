---
title: "Serviços de Domínio do Azure AD: Criar ou selecionar uma rede virtual | Microsoft Docs"
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
ms.date: 10/03/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9e933774e3b618b1584b4f24a0491eda49e42077


---
# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Criar ou selecionar uma rede virtual para os Serviços de Domínio do Azure AD
## <a name="guidelines-to-select-an-azure-virtual-network"></a>Diretrizes para selecionar uma Azure Virtual Network
> [!NOTE]
> **Antes de começar**: consulte [Considerações sobre o funcionamento em rede dos Serviços de Domínio do Azure AD](active-directory-ds-networking.md).
> 
> 

## <a name="task-2-create-an-azure-virtual-network"></a>Tarefa 2: Criar uma Azure Virtual Network
A tarefa de configuração seguinte é criar uma rede virtual do Azure e uma sub-rede dentro da mesma. O Azure AD Domain Services é ativado nesta sub-rede dentro da sua rede virtual. Se já tiver uma rede virtual existente que prefira utilizar, pode ignorar este passo.

> [!NOTE]
> Certifique-se de que a Azure Virtual Network que cria ou opta por utilizar com os Serviços de Domínio do Azure AD pertence a uma região do Azure que seja suportado pelos Serviços de Domínio do Azure AD. Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.
> 
> 

Tome nota do nome da rede virtual para que possa selecionar a rede virtual correta quando ativar os Serviços de Domínio do Azure AD num passo subsequente da configuração.

Execute os seguintes passos de configuração para criar uma Azure Virtual Network na qual gostaria de ativar os Serviços de Domínio do Azure AD.

1. Navegue para o **Portal Clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selecione o nó **Redes** no painel da esquerda.
   
    ![Nó de redes](./media/active-directory-domain-services-getting-started/networks-node.png)
3. Clique em **NOVO** no painel de tarefas na parte inferior da página.
   
    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. No nó **Serviços de Rede**, selecione **Virtual Network**.
5. Clique em **Criação Rápida** para criar uma rede virtual.
   
    ![Rede virtual – criação rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
6. Especifique um **Nome** para a sua rede virtual. Também pode optar por configurar o **Espaço de endereço** ou **Número máximo de VMs** para esta rede. Pode deixar a definição do **servidor DNS** definida como “Nenhum” por agora. Pode atualizá-la depois de ativar o Azure AD Domain Services.
7. Certifique-se de que seleciona uma região do Azure suportada no menu pendente **Localização**. Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.
8. Para criar a rede virtual, clique no botão **Criar uma Rede Virtual**.
   
    ![Crie uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. Depois de criar a rede virtual, selecione-a e clique no separador **CONFIGURAR**.
   
    ![Criar uma sub-rede](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Navegue para a secção **espaços de endereços da rede virtual**. Clique em **adicionar sub-rede** e especifique uma sub-rede com o nome **AaddsSubnet**. Clique em **Guardar** para criar a sub-rede.
    
    ![Crie uma sub-rede para o Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Tarefa 3 – Ativar os Serviços de Domínio do Azure AD
A próxima tarefa de configuração consiste em [ativar os Serviços de Domínio do Azure AD](active-directory-ds-getting-started-enableaadds.md).




<!--HONumber=Dec16_HO1-->


