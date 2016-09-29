<properties
    pageTitle="Criar um plano no Azure Stack | Microsoft Azure"
    description="Enquanto administrador de serviços, crie um plano que permita aos subscritores aprovisionar máquinas virtuais."
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


# Criar um plano no Azure Stack

Os [Planos](azure-stack-key-features.md#services-plans-offers-and-subscriptions) consistem em agrupamentos de um ou mais serviços. Enquanto fornecedor, pode criar planos para oferecer aos seus inquilinos. Por sua vez, os seus inquilinos subscrevem as suas ofertas para utilizar os planos e os serviços incluídos. Neste exemplo, vai criar um plano que inclui a computação, a rede e os fornecedores de recursos de armazenamento. Este procedimento vai permitir que os subscritores do plano aprovisionem máquinas virtuais.

1.  Num browser, navegue até https://portal.azurestack.local.

2.  [Inicie sessão](azure-stack-connect-azure-stack.md#log-in-as-a-service-administrator) no Portal do Azure Stack como administrador de serviços, introduza as suas credenciais (trata-se da conta criada no passo 5 da secção [Executar o script do PowerShell](azure-stack-run-powershell-script.md)) e, em seguida, clique em **Iniciar sessão**.

    Os administradores de serviços podem criar ofertas e planos e gerir utilizadores.

3.  Para criar um plano e uma oferta que os inquilinos possam subscrever, clique em **Novo**.

    ![](media/azure-stack-create-plan/image1.png)

4.  No painel Criar, clique em **Ofertas e Planos do Inquilino** e, em seguida, clique em **Plano**.

    ![](media/azure-stack-create-plan/image2.png)

5.  Preencha o **Nome a Apresentar** e o **Nome do Recurso**. O Nome a Apresentar é o nome amigável do plano. Apenas o administrador pode ver o Nome do Recurso. Trata-se do nome que o administrador utiliza para trabalhar com o plano como um recurso do Azure Resource Manager.

    ![](media/azure-stack-create-plan/image3.png)

6.  Selecione ou crie um novo **Grupo de Recursos** como contentor do plano. Por predefinição, todos os planos e ofertas farão parte de um grupo de recursos designado OffersAndPlans.

7.  Clique em **Serviços Oferecidos**, utilize a tecla Shift para selecionar os três fornecedores (**Fornecedor de Computação**, **Fornecedor de Armazenamento** e **Fornecedor de Rede**) e, em seguida, clique em **Selecionar**.

    ![](media/azure-stack-create-plan/image4.png)

8.  Clique em **Microsoft.Compute** e em **Precisa de Configuração**.

    ![](media/azure-stack-create-plan/image5.png)

9.  No painel **Definir Quotas**, aceite todas as predefinições, clique em **OK** e, em seguida, clique novamente em **OK**.

    ![](media/azure-stack-create-plan/image6.png)

10. Clique em **Microsoft.Network** e em **Precisa de Configuração**.

    ![](media/azure-stack-create-plan/image7.png)

11. No painel **Definir Quotas**, selecione todas as opções, clique em **OK** e, em seguida, clique novamente em **OK**.

    ![](media/azure-stack-create-plan/image8.png)

12. Clique em **Microsoft.Storage** e em **Precisa de Configuração**. Em seguida, no painel **Definir Quotas**, aceite todas as predefinições, clique em **OK**, clique novamente em **OK** e, em seguida, clique em **Criar** para criar o plano.

    ![](media/azure-stack-create-plan/image9.png)

13. O plano pode agora ser incluído numa oferta. Ver notificações ao clicar na campainha na parte superior direita.

    ![](media/azure-stack-create-plan/image10.png)

## Passos seguintes

[Criar uma oferta](azure-stack-create-offer.md)



<!--HONumber=Sep16_HO3-->


