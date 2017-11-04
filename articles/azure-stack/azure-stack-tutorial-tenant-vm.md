---
title: "Máquinas virtuais disponibilizar aos utilizadores de pilha do Azure | Microsoft Docs"
description: "Tutorial para disponibilizar as máquinas virtuais na pilha do Azure"
services: azure-stack
documentationcenter: 
author: vhorne
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f6fce4a3230c98295afb19e633bf2801c115831f
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Máquinas virtuais disponibilizar aos utilizadores de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Como um administrador da nuvem de pilha do Azure, pode criar ofertas podem subscrever os seus utilizadores (algumas vezes referidos como inquilinos). Utilizar a subscrição, os utilizadores podem consumir, em seguida, serviços de pilha do Azure.

Este artigo mostra como criar uma oferta e, em seguida, testá-lo. Para o teste, irá iniciar sessão no portal como um utilizador, subscrever a oferta e, em seguida, criar uma máquina virtual utilizando a subscrição.

O que aprenderá:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * A oferta de teste


Na pilha do Azure, os serviços são fornecidos aos utilizadores através de subscrições, ofertas e planos. Os utilizadores podem subscrever oferece várias. Ofertas podem ter um ou mais planos e planos podem ter um ou mais serviços.

![As subscrições, ofertas e planos](media/azure-stack-key-features/image4.png)

Para obter mais informações, consulte [conceitos na pilha do Azure e as funcionalidades da chave](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Criar uma oferta

Agora pode obter coisas pronto para os seus utilizadores. Quando inicia o processo, lhe primeiro para criar a oferta, em seguida, um plano e, finalmente, quotas.

3. **Criar uma oferta**

   Ofertas são grupos de um ou mais planos que Fornecedores aos utilizadores para comprar ou subscrever.

   a. [Inicie sessão no](azure-stack-connect-azure-stack.md) no portal como um administrador da nuvem e, em seguida, clique em **novo** > **oferece + planos** > **oferecem**.
   ![Nova oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. No **oferecem novo** secção, preencha **nome a apresentar** e **nome do recurso**e, em seguida, selecione o novo ou existente **grupo de recursos**. O Nome a Apresentar é o nome amigável da oferta. Apenas o operador da nuvem, pode ver o nome do recurso. Trata-se do nome que o administrador utiliza para trabalhar com a oferta como um recurso do Azure Resource Manager.

   ![Nome a apresentar](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Clique em **Base planos**e, no **plano** secção, clique em **adicionar** para adicionar um novo plano para a oferta.

   ![Adicione um plano](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. No **planear novo** secção, preencha **nome a apresentar** e **nome do recurso**. O nome de apresentação é o nome amigável do plano e que os utilizadores veem. Apenas o operador da nuvem, pode ver o nome do recurso. É o nome que utilizam os operadores da nuvem para funcionar com o plano como um recurso do Azure Resource Manager.

   ![Planear o nome a apresentar](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Clique em **serviços**, selecione **Microsoft. Compute**, **Network**, e **Microsoft**e, em seguida, clique em **Selecione**.

   ![Planear os serviços](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Clique em **Quotas**e, em seguida, selecione o primeiro serviço para o qual pretende criar uma quota. Para uma quota de IaaS, siga estes passos para os serviços de computação, rede e armazenamento.

   Neste exemplo, vamos criar primeiro uma quota para o serviço de computação. Na lista de espaço de nomes, selecione o **Microsoft. Compute** espaço de nomes e, em seguida, clique em **criar nova quota**.
   
   ![Criar nova quota](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. No **Criar quota** secção, escreva um nome para a quota e defina os parâmetros pretendidos para a quota e clique em **OK**.

   ![Nome de quota](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. Agora, para **Microsoft. Compute**, selecione a quota que criou.

   ![Selecione quota](media/azure-stack-tutorial-tenant-vm/image08.png)

   Repita estes passos para os serviços de armazenamento e de rede e, em seguida, clique em **OK** no **Quotas** secção.

   posso. Clique em **OK** no **novo plano** secção.

   j. No **plano** secção, selecione o novo plano e clique em **selecione**.

   k. No **oferta novo** secção, clique em **criar**. Verá uma notificação quando foi criada a oferta.

   l. No menu do dashboard, clique em **oferece** e, em seguida, clique na oferta que criou.

   m. Clique em **Alterar Estado** e, em seguida, em **Público**.

   ![Estado público](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Adicionar uma imagem

Pode aprovisionar as máquinas virtuais, tem de adicionar uma imagem do marketplace de pilha do Azure. Pode adicionar a imagem à sua escolha, incluindo as imagens de Linux, do Azure Marketplace.

Se estão a funcionar num cenário ligado e se registou a instância de pilha do Azure com o Azure, em seguida, pode transferir a imagem de VM do Windows Server 2016 do Azure Marketplace utilizando os passos descritos no [marketplace de transferência itens a partir do Azure a pilha de Azure](azure-stack-download-azure-marketplace-item.md) tópico.

Para obter informações sobre como adicionar itens diferentes para o mercado, consulte [o Azure Marketplace de pilha](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>A oferta de teste

Agora que criou uma oferta, pode testá-lo. Inicie sessão como um utilizador e subscrever a oferta e, em seguida, adicionar uma máquina virtual.

1. **Subscrever uma oferta**

   Agora, pode iniciar sessão no portal como um utilizador para subscrever uma oferta.

   a. Inicie sessão no portal de utilizador como um utilizador e clique em **obter uma subscrição**.
   - Para um sistema integrado, o URL varia com base na região e nome de domínio externo o operador e estarão disponíveis na https://portal formato. &lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se estiver a utilizar o Kit de desenvolvimento de pilha do Azure, o endereço de portal é https://portal.local.azurestack.external.

   ![Obter uma subscrição](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. No **nome a apresentar** campo, escreva um nome para a sua subscrição, clique em **oferecem**, clique das ofertas no **escolher uma oferta** secção e, em seguida, clique em  **Criar**.

   ![Criar uma oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Para ver a subscrição que criou, clique em **mais serviços**, clique em **subscrições**, em seguida, clique em sua nova subscrição.  

   Depois de subscrever uma oferta, atualize o portal para ver quais os serviços fazem parte da nova subscrição.

2. **Aprovisionar uma máquina virtual**

   Agora, pode iniciar sessão no portal como um utilizador para Aprovisionar uma máquina virtual utilizando a subscrição. 

   a. Inicie sessão no portal de utilizador como um utilizador.
      - Para um sistema integrado, o URL varia com base na região e nome de domínio externo o operador e estarão disponíveis na https://portal formato. &lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se estiver a utilizar o Kit de desenvolvimento de pilha do Azure, o endereço de portal é https://portal.local.azurestack.external.

   b.  No dashboard, clique em **novo** > **computação** > **Windows Server 2016 Datacenter Eval**e, em seguida, clique em **criar**.

   c. No **Noções básicas** secção, escreva um **nome**, **nome de utilizador**, e **palavra-passe**, escolha um **subscrição**, criar um **grupo de recursos** (ou selecione um existente) e, em seguida, clique em **OK**.

   d. No **escolher um tamanho** secção, clique em **A1 padrão**e, em seguida, clique em **selecione**.  

   e. No **definições** secção, clique em **rede Virtual**. No **escolha de rede virtual** secção, clique em **criar nova**. No **criar rede virtual** secção, aceite todas as predefinições e clique em **OK**. No **definições** secção, clique em **OK**.

   ![Criar a rede virtual](media/azure-stack-provision-vm/image04.png)

   f. No **resumo** secção, clique em **OK** para criar a máquina virtual.  

   g. Para ver a nova máquina virtual, clique em **todos os recursos**, em seguida, procure a máquina virtual e clique no respetivo nome.

    ![Todos os recursos](media/azure-stack-provision-vm/image06.png)

O que aprendeu neste tutorial:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * A oferta de teste

> [!div class="nextstepaction"]
> [Certifique-web, móveis e API apps disponíveis para os utilizadores de pilha do Azure](azure-stack-tutorial-app-service.md)