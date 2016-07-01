<properties
    pageTitle="Criar um laboratório no DevTest Labs | Microsoft Azure"
    description="Criar um novo laboratório no DevTest Labs para máquinas virtuais"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="tarcher"/>

# Criar um laboratório no Azure DevTest Labs

## Pré-requisitos

Para criar um laboratório, precisará de:

- Uma subscrição do Azure. Para obter informações sobre opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Tem de ser o proprietário da subscrição para criar o laboratório.
- Um Grupo de Recursos do Azure para o laboratório. Consulte [Descrição geral do Azure Resource Manager](../resource-group-overview.md) e [Controlo de Acesso baseado em Funções do Azure](../active-directory/role-based-access-control-configure.md).

## Criar um laboratório

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**.

1. Selecione **DevTest Labs** da lista.

1. No painel **DevTest Labs**, selecione **Adicionar**.

    ![Adicionar um laboratório](./media/devtest-lab-create-lab/add-lab-button.png)

1. No painel **Criar um DevTest Lab**:

    1. Introduza um **Nome de Laboratório** para o novo laboratório.
    1. Selecione a **Subscrição** para associar ao laboratório.
    1. Selecione uma **Localização** onde armazenar o laboratório.
    1. Selecione **Criar**.

    ![Painel Criar um laboratório](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

## Passos seguintes

Assim que tiver criado o seu laboratório, seguem-se alguns passos a ter em consideração:

- [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md).

- [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).

- [Criar um modelo de laboratório](devtest-lab-create-template.md).

- [Criar artefactos personalizados para as suas VMs](devtest-lab-artifact-author.md).

- [Adicionar uma VM com artefactos para um laboratório](devtest-lab-add-vm-with-artifacts.md).


<!--HONumber=Jun16_HO2-->


