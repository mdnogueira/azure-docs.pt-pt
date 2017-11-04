---
title: Delegar ofertas na pilha do Azure | Microsoft Docs
description: "Saiba como colocar outras pessoas responsável pela criação de ofertas e inscrever-se os utilizadores."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: alfredop
ms.openlocfilehash: 1a1f2789076b610ee557bde5981a331c55cc1c95
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="delegate-offers-in-azure-stack"></a>Delegar ofertas no Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Como operador a pilha do Azure, muitas vezes, pretende colocar outras pessoas responsável pela criação de ofertas e inscrever-se os utilizadores. Por exemplo, se for um fornecedor de serviços, poderá revendedores inscrever-se os clientes e geri-los em seu nome. Ou, se a parte de um grupo central de TI de uma empresa, é aconselhável subsidiárias inscrever-se os utilizadores sem a intervenção do utilizador.

Delegação ajuda-o a estas tarefas, tornando a possível aceder e gerir mais utilizadores pode diretamente. A ilustração seguinte mostra um nível de delegação, mas a pilha do Azure suporta vários níveis. Fornecedores de delegado (DPs) por sua vez podem delegar a outros fornecedores, até cinco níveis.

![Níveis de delegação](media/azure-stack-delegated-provider/image1.png)

Os operadores do Azure da pilha podem delegar a criação de ofertas e os utilizadores a outros utilizadores utilizando a funcionalidade de delegação.

## <a name="roles-and-steps-in-delegation"></a>Funções e passos delegação
Para compreender a delegação, tenha em atenção que existem três funções envolvidos:

* O *operador de pilha do Azure* gere a infraestrutura de pilha do Azure, cria um modelo de oferta e delega outras pessoas para oferecê-lo aos respetivos utilizadores.

* Os operadores do Azure pilha delegados são denominados *delegado fornecedores*. Estes podem pertencer a outras organizações, tal como outros utilizadores do Azure Active Directory (Azure AD).

* *Os utilizadores* Inscreva-se a ofertas e utilizá-los para gerir as respetivas cargas de trabalho de criação de VMs, armazenamento de dados e assim sucessivamente.

Como é mostrado no seguinte gráfico, existem dois passos para configurar a delegação.

1. *Identificar os fornecedores de delegado*. Fazê-lo ao subscrevê-los para uma oferta com base num plano que contém apenas o serviço de subscrições. Os utilizadores que subscrevem esta oferta adquirir algumas das capacidades do operador de pilha do Azure, incluindo a capacidade de expandir ofertas e sessão dos utilizadores para lhes.

2. *Delegar uma oferta para o fornecedor de delegado*. Esta oferta funciona como um modelo para o que pode oferecer o fornecedor de delegado. O fornecedor de delegado agora poderá demorar a oferta. Escolha um nome para o mesmo (mas não alterar os respetivos serviços e quotas) e oferecem-a clientes.

![Criar o fornecedor de delegado e ativá-los inscrever-se os utilizadores](media/azure-stack-delegated-provider/image2.png)

Para agir como delegados fornecedores, os utilizadores necessitam estabelecer uma relação com o fornecedor principal. Por outras palavras, precisam de criar uma subscrição. Neste cenário, esta subscrição identifica os fornecedores de delegado como tendo o direito de ofertas presentes em nome do fornecedor de principal.

Depois desta relação foi estabelecida, o operador de pilha do Azure pode delegar uma oferta para o fornecedor de delegado. O fornecedor de delegado agora é possível efetuar a oferta, mude o nome (mas não alterar o respetivo substance) e oferecem-la para que os seus clientes.

As secções seguintes descrevem como estabelecer um fornecedor de delegado, delegar uma oferta e certifique-se de que os utilizadores podem inscrever-se-lo.

## <a name="set-up-roles"></a>Definir funções

Para ver um fornecedor de delegado no trabalho, terá de Azure adicional contas de anúncios, para além da sua conta de operador de pilha do Azure. Se não tivê-los, crie as duas contas. As contas podem pertencer a qualquer utilizador do Azure AD. Iremos consultá-las como o fornecedor de delegado e utilizador.

| **Função** | **Direitos organizacionais** |
| --- | --- |
| Fornecedor de delegado |Utilizador |
| Utilizador |Utilizador |

## <a name="identify-the-delegated-providers"></a>Identificar os fornecedores de delegado
1. Inicie sessão como um operador de pilha do Azure.

2. Crie a oferta que permite aos utilizadores para se tornarem delegados fornecedores:
   
   a.  [Criar um plano](azure-stack-create-plan.md).
       Este plano deve incluir apenas o serviço de subscrições. Neste artigo, vamos utilizar um plano chamado **PlanForDelegation**.
   
   b.  [Criar uma oferta](azure-stack-create-offer.md) com base neste plano. Neste artigo, utilizamos uma oferta chamada **OfferToDP**.
   
   c.  Depois de concluída a criação da oferta, adicione o fornecedor de delegado como um subscritor para esta oferta. Fazê-lo ao selecionar **subscrições** > **adicionar** > **nova subscrição de inquilino**.
   
   ![Adicione o fornecedor de delegado como um subscritor](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> Com todas as ofertas de pilha do Azure, que tem a opção de efetuar a oferta utilizadores públicos e permitir que o Inscreva-se, ou manter privada e permitir que o operador de pilha do Azure, gerir a inscrição. Fornecedores de delegado são, normalmente, um pequeno grupo. Pretende controlar quem admitted, para que manter esta oferta privada faz sentido na maioria dos casos.
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>Operador de pilha do Azure cria a oferta delegada

Agora estabelecer o fornecedor de delegado. O passo seguinte consiste em criar o plano e a oferta que vai delegar e que irão utilizar os seus clientes. É uma boa ideia para definir esta oferta exatamente como pretende que os clientes para vê-lo porque o fornecedor de delegado não poderá alterar os planos e quotas inclui.

1. Como um operador de pilha do Azure, [criar um plano](azure-stack-create-plan.md) e [uma oferta](azure-stack-create-offer.md) baseados nas mesmas. Para este artigo, utilizamos uma oferta chamada **DelegatedOffer.**
   
   > [!NOTE]
   > Esta oferta não tem de ser público. Se escolher, pode tornar público. Na maioria dos casos, no entanto, apenas pretende fornecedores de delegado para ter acesso à mesma. Depois de delegar uma oferta privada, tal como descrito nos passos seguintes, o fornecedor de delegado tem acesso ao mesmo.
   > 
   > 
1. Delegar a oferta. Aceda a **DelegatedOffer.** Em seguida, no **definições** painel, selecione **delegado fornecedores** > **adicionar**.

2. Selecione a subscrição para o fornecedor de delegado a partir da caixa de lista pendente e, em seguida, selecione **delegado**.

> ![Adicionar um fornecedor de delegado](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>Fornecedor de delegado customizes a oferta

Inicie sessão no portal de utilizador como o fornecedor de delegado. Em seguida, crie uma nova oferta utilizando a oferta de delegado como um modelo.

1. Selecione **novo** > **inquilino oferece + planos** > **oferecem**.

    ![Criar uma nova oferta](media/azure-stack-delegated-provider/image5.png)


1. Atribua um nome para a oferta. Aqui vamos escolher **ResellerOffer**. Selecione a oferta delegada em que a base-lo e, em seguida, selecione **criar**.
   
   ![Atribua um nome](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > Tenha em atenção a diferença em comparação comparada a oferta de criação como experiente pela operadora de rede de pilha do Azure. O fornecedor de delegado não construir a oferta de planos base e planos de suplemento. Que podem apenas escolher as ofertas que foram delegadas aos mesmos e não é possível efetuar alterações a esses ofertas.

1. Tornar a oferta público selecionando **procurar**e, em seguida, **oferece**. Selecione a oferta e, em seguida, selecione **alteração de estado**.

2. O fornecedor de delegado expõe estas ofertas através dos seus próprios portal URL. Estas ofertas estão visíveis apenas através do portal do delegado. Para localizar e alterar este URL:
   
    a.  Selecione **procurar** > **mais serviços** >  **subscrições**. Em seguida, selecione a subscrição do fornecedor de delegado. No nosso caso, tem **DPSubscription** > **propriedades**.
   
    b.  Copie o portal de URL para uma localização separada, como o bloco de notas.
   
    ![Selecione a subscrição do fornecedor de delegado](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   Acabou de criar uma oferta de delegado como um fornecedor de delegado. Terminar a sessão porque o fornecedor de delegado. Feche a janela de browser que estiver a utilizar.

## <a name="sign-up-for-the-offer"></a>Inscreva-se a oferta
1. Numa nova janela do browser, aceda ao portal do delegado URL que guardou no passo anterior. Inicie sessão no portal como um utilizador. 
   
   >[!NOTE]
   > Utilize o portal de delegado para este passo. As delegado ofertas caso contrário, não estão visíveis.

2. No dashboard, selecione **obter uma subscrição**. Consulte a que apenas as ofertas de delegado que foram criadas pelo fornecedor de delegado são apresentadas ao utilizador:

> ![Ver e selecionar ofertas](media/azure-stack-delegated-provider/image8.png)
> 
> 

O processo de delegação de oferta está concluído. O utilizador pode agora inscrever-se esta oferta por obter uma subscrição para o mesmo.

## <a name="multiple-tier-delegation"></a>Delegação de várias camadas

Delegação de várias camadas que permite que o fornecedor de delegado delegar a oferta para outras entidades. Isto permite que, por exemplo, a criação de canais de revendedor mais aprofundadas, na qual o fornecedor que está a gerir o Azure pilha delega uma oferta para um distribuidor. Esse distribuidor, por sua vez, os delegados a um revendedor. Pilha do Azure suporta até cinco níveis de delegação.

Para criar várias camadas de delegação de oferta, o fornecedor de delegado por sua vez delega a oferta ao próximo fornecedor. O processo é o mesmo para o fornecedor de delegado que estava para o operador de pilha do Azure (consulte [operador de pilha do Azure cria a oferta delegada](#cloud-operator-creates-the-delegated-offer)).

## <a name="next-steps"></a>Passos seguintes
[Aprovisionar uma VM](azure-stack-provision-vm.md)

