---
title: "Gerir as fórmulas para criar as VMs do Azure DevTest Labs | Microsoft Docs"
description: "Saiba como atualizar e remover as fórmulas de Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfdab5def50158f9b764bbb1e50c2624cc6d5fb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-devtest-labs-formulas"></a>Gerir as fórmulas de Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Este artigo ilustra como criar uma fórmula de uma base (imagem personalizada, imagem do Marketplace ou outra fórmula) ou uma VM existente. Este artigo também orienta-o gestão de fórmulas existentes.

## <a name="create-a-formula"></a>Crie uma fórmula
Qualquer pessoa com DevTest Labs *utilizadores* permissões é capaz de criar as VMs com uma fórmula como base. Existem duas formas de criar as fórmulas: 

* A partir de uma base - utilize quando pretender definir todas as características da fórmula.
* A partir de um laboratório existente VM - utilize-a quando pretende criar uma fórmula com base nas definições de VM existente.

Para obter mais informações sobre como adicionar utilizadores e permissões, consulte [adicionar proprietários e os utilizadores no Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Crie uma fórmula a partir de uma base
Os seguintes passos guiá-lo durante o processo de criação de uma fórmula de uma imagem personalizada, imagem do Marketplace ou outra fórmula.

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.

3. Na lista de laboratórios, selecione o laboratório pretendido.  

4. No painel do laboratório, selecione **fórmulas (bases reutilizáveis)**.
   
    ![Menu fórmula](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. No **fórmulas** painel, selecione **+ adicionar**.
   
    ![Adicionar uma fórmula](./media/devtest-lab-create-formulas/add-formula.png)

6. No **escolher uma base** painel, selecione base (imagem personalizada, imagem do Marketplace ou fórmula) a partir do qual pretende criar a fórmula.
   
    ![Lista de base](./media/devtest-lab-create-formulas/base-list.png)

7. No **criar fórmula** painel, especifique os seguintes valores:
   
    * **Nome da fórmula** -introduza um nome para a sua fórmula. Este valor é apresentado na lista de imagens base quando criar uma VM. O nome é validado como a escrevê-lo e, se não é válido, uma mensagem indica os requisitos para um nome válido.
    * **Descrição** -introduza uma descrição com significado para sua fórmula. Este valor está disponível no menu de contexto a fórmula quando criar uma VM.
    * **Nome de utilizador** -introduza um nome de utilizador que é concedido privilégios de administrador.
    * **Palavra-passe** - introduza - ou selecione na lista pendente - um valor que estão associado com o segredo (palavra-passe) que pretende utilizar para o utilizador especificado. Para obter mais informações sobre os segredos, consulte [Azure DevTest Labs: arquivo pessoal do segredo](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/).
    * **Tipo de disco de máquina virtual** - Especifique um HDD (unidade de disco rígido) ou SSD (unidade de estado sólido) para indicar o tipo de disco de armazenamento é permitido para as máquinas virtuais aprovisionadas com esta imagem de base.
    * * *: Máquina Virtual tamanho * * - selecione um dos itens de predefinida que especifique os núcleos de processador, o tamanho da RAM e o tamanho de disco rígido da VM para criar. 
    * **Artefactos** - Selecione para abrir o **adicionar artefactos** painel, na qual pode selecionar e configurar os artefactos que pretende adicionar à imagem de base. Para mais informações sobre os artefactos, consulte [artefactos de gerir a VM no Azure DevTest Labs](./devtest-lab-add-vm-with-artifacts.md).
    * **As definições avançadas** - Selecione para abrir o **avançadas** painel onde configurou as seguintes definições:
        * **Rede virtual** -especifique a rede virtual pretendida.
        * **Sub-rede** -especifique a sub-rede pretendida.    
        * **Configuração do endereço IP** -especificar se pretende que os endereços de Public, Private ou IP partilhado. Para obter mais informações sobre os endereços IP partilhados, consulte [compreender partilhado endereços IP no Azure DevTest Labs](./devtest-lab-shared-ip.md).
        * **Tornar esta máquina claimable** -tornar uma máquina "claimable" significa que mesmo não será atribuído propriedade no momento da criação. Em vez disso, os utilizadores de laboratório poderá assumir a propriedade ("afirmação") na máquina no painel de laboratório.     
    * **Imagem** -este campo apresenta o nome da imagem base selecionado no painel anterior. 
     
       ![Criar a fórmula](./media/devtest-lab-create-formulas/create-formula.png)

8. Selecione **criar** para criar a fórmula.

9. Quando a fórmula tiver sido criada, é apresentado na lista no **fórmulas** painel.

### <a name="create-a-formula-from-a-vm"></a>Crie uma fórmula de uma VM
Os seguintes passos guiá-lo durante o processo de criação de uma fórmula com base numa VM existente. 

> [!NOTE]
> Para criar uma fórmula de uma VM, a VM deve ter sido criada após 30 de Março de 2016. 
> 
> 

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.  
4. No laboratório de **descrição geral** painel, selecione a partir do qual pretende criar a fórmula VM.
   
    ![Laboratórios VMs](./media/devtest-lab-create-formulas/my-vms.png)
5. No painel da VM, selecione **criar fórmula (base reutilizável)**.
   
    ![Criar a fórmula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. No **criar fórmula** painel, introduza um **nome** e **Descrição** para a fórmula de novo.
   
    ![Criar painel fórmula](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Selecione **OK** para criar a fórmula.

## <a name="modify-a-formula"></a>Modificar uma fórmula
Para modificar uma fórmula, siga estes passos:

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.  
4. No painel do laboratório, selecione **fórmulas (bases reutilizáveis)**.
   
    ![Menu fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. No **fórmulas laboratório** painel, selecione a fórmula que pretende modificar.
6. No **atualizar fórmula** painel, efetue as edições pretendidas e selecione **atualização**.

## <a name="delete-a-formula"></a>Eliminar uma fórmula
Para eliminar uma fórmula, siga estes passos:

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.  
4. No laboratório **definições** painel, selecione **fórmulas**.
   
    ![Menu fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. No **fórmulas laboratório** painel, selecione o botão de reticências à direita da fórmula que pretende eliminar.
   
    ![Menu fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. No menu de contexto a fórmula, selecione **eliminar**.
   
    ![Menu de contexto de fórmulas](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecione **Sim** para a caixa de diálogo de confirmação de eliminação.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Mensagens de blogue relacionados
* [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passos seguintes
Assim que tiver criado uma fórmula para utilização quando criar uma VM, o passo seguinte consiste em [adicionar uma VM para o laboratório](devtest-lab-add-vm-with-artifacts.md).

