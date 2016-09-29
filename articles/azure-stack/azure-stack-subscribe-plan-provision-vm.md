<properties
    pageTitle="Subscrever uma oferta e aprovisionar uma VM no Azure Stack (inquilino) | Microsoft Azure"
    description="Como inquilino, saiba como subscrever uma oferta e aprovisionar uma VM no Azure Stack."
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
    ms.date="08/15/2016"
    ms.author="erikje"/>


# Subscrever uma oferta

Agora que [criou uma oferta](azure-stack-create-offer.md), verifique se os seus inquilinos podem criar a subscrição.

1.  No computador da Prova de Conceito do Azure Stack, inicie sessão `https://portal.azurestack.local` como [inquilino](azure-stack-connect-azure-stack.md#log-in-as-a-tenant) e clique em **Obter uma Subscrição**.

    ![](media/azure-stack-subscribe-plan-provision-vm/image1.png)

2.  No campo **Nome a Apresentar**, introduza um nome para a subscrição.

    ![](media/azure-stack-subscribe-plan-provision-vm/image2.png)

3.  Clique em **Oferta** e verifique se a oferta que criou se encontra no painel **Escolher uma oferta**. Clique nessa oferta, em **Selecionar** e, em seguida, em **OK**.  

    ![](media/azure-stack-subscribe-plan-provision-vm/image3.png)

4.  Para ver a subscrição que acabou de criar, clique em **Procurar** e, em seguida, em **Subscrições**.  

    ![](media/azure-stack-subscribe-plan-provision-vm/image4.png)

Após subscrever uma oferta, aguarde até o estado da subscrição ser InSync. Em seguida, atualize o portal para ver quais os serviços que fazem parte da nova subscrição.




## Passos seguintes

[Contas de armazenamento](azure-stack-provision-storage-account.md)



<!--HONumber=Sep16_HO3-->


