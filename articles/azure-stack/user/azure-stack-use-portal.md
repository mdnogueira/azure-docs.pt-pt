---
title: "Através do portal do Azure pilha | Microsoft Docs"
description: Saiba como aceder e utilizar o portal de utilizador na pilha do Azure.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 63c270affca31d3db7e03116f5e287d8569b0dae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-stack-portal"></a>Utilizar o portal de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Como um consumidor de serviços de pilha do Azure, pode utilizar o portal do Azure pilha para subscrever ofertas públicas e utilizar os serviços que são disponibilizados através esses ofertas. Se utilizou o portal do Azure antes, já estiver familiarizado com a interface de utilizador.

## <a name="access-the-portal"></a>Aceder ao portal

O operador de pilha do Azure (um fornecedor de serviços ou um administrador na sua organização), permitirá sabe o URL correto para aceder ao portal. 

- Para um sistema integrado, o URL varia com base na região e nome de domínio externo o operador e estarão disponíveis na https://portal formato. &lt; *região*&gt;.&lt; *FQDN*&gt;.
- Se estiver a utilizar o Kit de desenvolvimento de pilha do Azure, o endereço de portal é https://portal.local.azurestack.external.

![Captura de ecrã do portal de utilizador de pilha do Azure](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalizar o dashboard

O dashboard contém um conjunto de mosaicos predefinidos. Pode clicar em **editar dashboard** para modificar o dashboard predefinido ou clique em **novo dashboard** adicionar dashboards personalizados. Pode facilmente adicionar mosaicos ao dashboard. Por exemplo, pode clicar em **novo**, faça duplo clique **computação**e, em seguida, clique em **afixar ao dashboard**.

## <a name="create-subscription-and-browse-available-resources"></a>Criar subscrição e a procurar recursos disponíveis
 
Se ainda não tiver uma subscrição, a primeira coisa que precisa de fazer é subscrever uma oferta. Depois disso, pode procurar os recursos que estão disponíveis para si. Para procurar e criar recursos, efetue qualquer uma das seguintes:

- Clique em de **Marketplace** mosaico no dashboard. 
- No **todos os recursos** mosaico, clique em **criar recursos**.
- No painel de navegação esquerdo, clique em **novo**.

## <a name="learn-how-to-use-available-services"></a>Saiba como utilizar os serviços disponíveis

Se necessitar de orientação sobre como utilizar os serviços disponíveis, poderão ocorrer diferentes opções disponíveis para si.

- Sua organização ou o fornecedor de serviços pode fornecer a sua própria documentação. Isto é especialmente verdade se oferecem serviços personalizados ou aplicações.
- Aplicações de terceiros tem sua própria documentação.
- Para os serviços do Azure consistente, recomendamos vivamente que reveja a documentação de pilha do Azure pela primeira vez. Para aceder à documentação do utilizador a pilha do Azure, clique no ícone de ajuda e, em seguida, clique em **ajuda + suporte**.
 
    ![Captura de ecrã da opção de ajuda e suporte na IU](media/azure-stack-use-portal/HelpAndSupport.png)

    Em particular, sugerimos que reveja os artigos seguintes para começar a utilizar:

    - [Considerações da chave: utilizar os serviços ou na criação de aplicações para a pilha do Azure](azure-stack-considerations.md)
    - Na secção "Utilizar serviços" da documentação, verá cada serviço consistentes com o Azure listado. Não há um tópico "Considerações" para cada serviço, que descreve as diferenças entre o serviço disponibilizadas no Azure e o mesmo serviço oferecido na pilha do Azure. Por exemplo, consulte [considerações de VM](azure-stack-vm-considerations.md). Poderão existir outras informações na secção "Utilizar serviços" que seja exclusiva à pilha do Azure. 
     
      Pode utilizar a documentação do Azure como referência geral para um serviço, mas tem de ser informados destas diferenças. Compreender que a documentação contém ligações no **tutoriais de início rápido** mosaico ponto para a documentação do Azure.

## <a name="get-support"></a>Obter suporte

Se precisar de suporte adicional, contacte o seu fornecedor de serviço ou organização para obter assistência. 

Se estiver a utilizar o Kit de desenvolvimento de pilha do Azure, o [fórum de Azure pilha](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) é o único método de suporte.

## <a name="next-steps"></a>Passos seguintes

[Considerações da chave: utilizar os serviços ou na criação de aplicações para a pilha do Azure](azure-stack-considerations.md)
