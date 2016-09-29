<properties
    pageTitle="Criar uma oferta no Azure Stack | Microsoft Azure"
    description="Enquanto administrador de serviços, saiba como criar uma oferta para os seus inquilinos do Azure Stack."
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
    ms.date="05/25/2016"
    ms.author="erikje"/>


# Criar uma oferta no Azure Stack

As [Ofertas](azure-stack-key-features.md#services-plans-offers-and-subscriptions) são grupos de um ou mais planos que os fornecedores apresentam aos inquilinos para comprarem (subscrever). Neste exemplo, vai criar uma oferta que inclui o [plano que criou](azure-stack-create-plan.md) no último passo. Este procedimento vai permitir que os subscritores da oferta aprovisionem máquinas virtuais.

1.  [Inicie sessão](azure-stack-connect-azure-stack.md#log-in-as-a-service-administrator) no portal como administrador de serviços.
    ![](media/azure-stack-create-offer/image1.png)

2.  Clique em **Novo**.

3.  Clique em **Ofertas e Planos do Inquilino** e, em seguida, clique em **Oferta**.
    ![](media/azure-stack-create-offer/image2.png)

4.  No painel **Nova Oferta**, faça o seguinte:

    1.  Preencha o **Nome a Apresentar** e o **Nome do Recurso**. O Nome a Apresentar é o nome amigável da oferta. Apenas o administrador pode ver o Nome do Recurso. Trata-se do nome que o administrador utiliza para trabalhar com a oferta como um recurso do Azure Resource Manager.

    2.  Selecione um novo **Grupo de Recursos** ou um grupo existente.

        ![](media/azure-stack-create-offer/image3.png)

5.  Clique em **Planos base** e, no painel **Plano**, selecione os planos que pretende incluir na oferta e, em seguida, clique em **Selecionar**. Clique em **Criar** para criar a oferta.

    ![](media/azure-stack-create-offer/image4.png)

6.  Clique em **Alterar Estado** e, em seguida, em **Público**.
Os planos e as ofertas têm de ser públicos para que os inquilinos possam ter a vista completa aquando da subscrição. Se um plano for privado e a oferta pública, os inquilinos poderão obter a oferta, mas não poderão ver os detalhes do plano. Os planos e as ofertas têm três opções:

    -   **Públicos**: visíveis para os inquilinos.

    -   **Privados**: apenas visíveis para os administradores do serviço. Útil durante a elaboração do plano ou da oferta, ou se o administrador de serviços pretender aprovar cada subscrição.

    -   **Fora de Utilização**: fechados a novos subscritores. O administrador de serviços pode utilizar a opção Fora de Utilização para evitar futuras subscrições, sem afetar os atuais subscritores.

    ![](media/azure-stack-create-offer/image6.png)

As alterações ao plano ou à oferta não são imediatamente visíveis para o inquilino. Para ver as alterações, o estado da subscrição tem de ser Em Sincronização e, em seguida, o inquilino deve atualizar o portal ou iniciar/terminar sessão.

Mesmo depois de ter sido criada uma subscrição adicional e o estado estar Em Sincronização, poderá ter de terminar/iniciar sessão para ver a nova subscrição no “Seletor de subscrições” ao criar novos recursos/grupos de recursos.

## Passos seguintes

[Subscrever uma oferta e aprovisionar uma VM](azure-stack-subscribe-plan-provision-vm.md)



<!--HONumber=Sep16_HO3-->


