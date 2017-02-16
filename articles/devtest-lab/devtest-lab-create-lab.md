---
title: "Criar um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Criar um laboratório no Azure DevTest Labs para máquinas virtuais"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 29c4c2a2818468a2fa8360eebd4b653bdcbbde19
ms.openlocfilehash: 0749d371466226343227c79db544a8e3dca0cca8


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Criar um laboratório no Azure DevTest Labs
## <a name="prerequisites"></a>Pré-requisitos
Para criar um laboratório, precisa de:

* Uma subscrição do Azure. Para obter informações sobre opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Tem de ser o proprietário da subscrição para criar o laboratório.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Passos para criar um laboratório no Azure DevTest Labs
Os passos seguintes mostram como utilizar o portal do Azure para criar um laboratório no Azure DevTest Labs. 

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais serviços**, e, em seguida, selecione **DevTest Labs** na lista.
3. No painel **DevTest Labs**, selecione **Adicionar**.
   
    ![Adicionar um laboratório](./media/devtest-lab-create-lab/add-lab-button.png)
4. No painel **Criar um DevTest Lab**:
   
   1. Introduza um **Nome de Laboratório** para o novo laboratório.
   2. Selecione a **Subscrição** para associar ao laboratório.
   3. Selecione uma **Localização** onde armazenar o laboratório.
   4. Selecione **Encerramento automático** para especificar se pretende ativar e definir os parâmetros para o encerramento automático de todas as VMs do laboratório. 
   5. Selecione **Criar**.
      
      ![Painel Criar um laboratório](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que tiver criado o seu laboratório, seguem-se alguns passos a ter em consideração:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md).
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).
* [Criar um modelo de laboratório](devtest-lab-create-template.md).
* [Criar artefactos personalizados para as suas VMs](devtest-lab-artifact-author.md).
* [Adicionar uma VM com artefactos para um laboratório](devtest-lab-add-vm-with-artifacts.md).




<!--HONumber=Dec16_HO3-->


