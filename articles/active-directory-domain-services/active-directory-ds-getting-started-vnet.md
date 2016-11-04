---
title: 'Serviços de Domínio do Azure AD: Criar ou selecionar uma rede virtual | Microsoft Docs'
description: Introdução aos Serviços de Domínio do Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: maheshu

---
# Criar ou selecionar uma rede virtual para os Serviços de Domínio do Azure AD
## Diretrizes para selecionar uma Azure Virtual Network
> [!NOTE]
> **Antes de começar**: consulte [Considerações sobre o funcionamento em rede dos Serviços de Domínio do Azure AD](active-directory-ds-networking.md).
> 
> 

## Tarefa 2: Criar uma Azure Virtual Network
A próxima tarefa de configuração consiste em criar uma Azure Virtual Network na qual gostaria de ativar os Serviços de Domínio do Azure AD. Se já tiver uma rede virtual existente que prefira utilizar, pode ignorar este passo.

> [!NOTE]
> Certifique-se de que a Azure Virtual Network que cria ou opta por utilizar com os Serviços de Domínio do Azure AD pertence a uma região do Azure que seja suportado pelos Serviços de Domínio do Azure AD. Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.
> 
> 

Tome nota do nome da rede virtual para que possa selecionar a rede virtual correta quando ativar os Serviços de Domínio do Azure AD num passo subsequente da configuração.

Execute os seguintes passos de configuração para criar uma Azure Virtual Network na qual gostaria de ativar os Serviços de Domínio do Azure AD.

1. Navegue para o **Portal Clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selecione o nó **Redes** no painel da esquerda.
3. Clique em **NOVO** no painel de tarefas na parte inferior da página.
   
    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. No nó **Serviços de Rede**, selecione **Virtual Network**.
5. Clique em **Criação Rápida** para criar uma rede virtual.
   
    ![Rede virtual – criação rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
6. Especifique um **Nome** para a sua rede virtual. Também pode optar por configurar o **Espaço de endereço** ou **Número máximo de VMs** para esta rede. Pode deixar a definição do servidor DNS definida como 'Nenhuma' por agora. Esta definição será atualizada após a ativação dos Serviços de Domínio do Azure AD.
7. Certifique-se de que seleciona uma região do Azure suportada no menu pendente **Localização**. Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.
8. Para criar a rede virtual, clique no botão **Criar uma Rede Virtual**.
   
    ![Crie uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

<br>

## Tarefa 3 – Ativar os Serviços de Domínio do Azure AD
A próxima tarefa de configuração consiste em [ativar os Serviços de Domínio do Azure AD](active-directory-ds-getting-started-enableaadds.md).

<!--HONumber=Sep16_HO3-->


