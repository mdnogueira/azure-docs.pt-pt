<properties
    pageTitle="Ligar à Prova de Conceito do Microsoft Azure Stack | Microsoft Azure"
    description="Saiba como ligar ao portal da Prova de Conceito do Azure Stack como administrador de serviços ou inquilino."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/01/2016"
    ms.author="erikje"/>


# Iniciar sessão na máquina virtual da Prova de Conceito do Azure Stack

Pode iniciar sessão na máquina virtual da Prova de Conceito do Azure Stack como

- [**administrador de serviços**](#log-in-as-a-service-administrator) para gerir fornecedores de recursos, ofertas, planos, serviços, quotas e preços do inquilino.

ou

- [**inquilino**](#log-in-as-a-tenant) para aprovisionar, monitorizar e gerir os serviços que subscreveu, como Aplicações Web, armazenamento e máquinas virtuais.

## Iniciar sessão como administrador de serviços

1.  Inicie sessão no computador físico da Prova de Conceito do Azure Stack.

2.  Faça duplo clique no ícone do ambiente de trabalho **ClientVM.AzureStack.local.rdp** para abrir uma Ligação ao Ambiente de Trabalho Remoto na máquina virtual do cliente.
 
    ![](media/azure-stack-connect-azure-stack/clientvmazurestacklocalicon.png)
    
    Este procedimento utiliza automaticamente a conta AzureStack\\AzureStackUser que foi criada pelo script de implementação. Utilize a palavra-passe de administrador que indicou no passo 5 do processo de script no pedido **Introduzir a palavra-passe do administrador incorporado**.

3.  No ambiente de trabalho ClientVM.AzureStack.local, faça duplo clique no ícone **Portal da Prova de Conceito do Microsoft Azure Stack** (https://portal.azurestack.local/) para abrir o [portal](azure-stack-key-features.md#portal).

    ![](media/azure-stack-connect-azure-stack/microsoftazurestackpocprtalicon.png)

4.  Inicie sessão com a conta de administrador de serviços.

## Iniciar sessão como um inquilino

Um administrador de serviços pode iniciar sessão como um inquilino para testar os planos, as ofertas e as subscrições que os seus inquilinos poderão utilizar.
Se ainda não tiver uma conta, deverá [Criar uma conta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sessão.

1.  Inicie sessão no computador físico do Azure Stack.

2.  Faça duplo clique no ícone do ambiente de trabalho **ClientVM.AzureStack.local.rdp** para abrir uma Ligação ao Ambiente de Trabalho Remoto na máquina virtual do cliente. 

    ![](media/azure-stack-connect-azure-stack/clientvmazurestacklocalicon.png)

    Este procedimento utiliza automaticamente a conta AzureStack\\AzureStackUser que foi criada pelo script de implementação. Utilize a palavra-passe de administrador que indicou no passo 5 do processo de script no pedido **Introduzir a palavra-passe do administrador incorporado**.

3.  No ambiente de trabalho ClientVM.AzureStack.local, faça duplo clique no ícone **Portal da Prova de Conceito do Microsoft Azure Stack** (https://portal.azurestack.local/) para abrir o [portal](azure-stack-key-features.md#portal).

    ![](media/azure-stack-connect-azure-stack/microsoftazurestackpocprtalicon.png)

4.  Inicie sessão com uma conta de inquilino.

O RDP poderá restringir o número de utilizadores que podem aceder ao anfitrião físico da Prova de Conceito do Microsoft Azure. Para permitir vários utilizadores, veja [Ativar ligações simultâneas de vários utilizadores](azure-stack-enable-multiple-concurrent-users.md).

## Passos seguintes

[Primeiras tarefas](azure-stack-first-scenarios.md)



<!--HONumber=Sep16_HO3-->


