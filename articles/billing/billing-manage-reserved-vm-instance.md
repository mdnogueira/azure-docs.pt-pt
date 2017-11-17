---
title: "Gerir instâncias de Máquina Virtual reservada do Azure | Microsoft Docs"
description: "Saiba como pode alterar o âmbito da subscrição e gerir o acesso para instâncias de VM do Azure reservado."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: e23eea52ff5d27beacf938a1ef153172e24f1aee
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-reserved-virtual-machine-instances"></a>Gerir instâncias de Máquina Virtual reservada

Depois de comprar uma instância de VM reservados do Azure, pode pretender aplicar a reserva para uma subscrição diferente daquela especificado durante a compra. Em alternativa, se as máquinas virtuais correspondentes estiver a executar em várias subscrições, poderá pretender alterar o âmbito da reserva partilhado. Para maximizar o desconto de reserva, certifique-se de que corresponde ao número de instâncias comprou os atributos e o número de máquinas virtuais que terá de executar. Para saber mais sobre as instâncias de Máquina Virtual reservado, consulte o artigo [poupar dinheiro pelo previamente pagar para máquinas virtuais do Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Alterar o âmbito de uma reserva
 Os descontos de reserva se aplica a máquinas virtuais que corresponde ao seu reserva e são executados dentro do âmbito de reserva. O âmbito de uma reserva de pode ser única subscrição ou todas as subscrições do contexto de faturação. Se definir o âmbito para subscrição único, a reserva é correspondida a máquinas virtuais em execução na subscrição selecionada. Se definir o âmbito para correspondências partilhadas, do Azure a reserva para máquinas virtuais que são executados em todas as subscrições dentro do contexto de faturação. O contexto de faturação é dependente da subscrição utilizada para comprar a reserva. Para obter mais informações, consulte [pré-pagamento para VMs com instâncias de VM reservado](https://go.microsoft.com/fwlink/?linkid=861721).

Para atualizar o âmbito de uma reserva de: 
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **mais serviços** > **reservas**.
3. Selecione a reserva.
4. Selecione **definições** > **configuração**.
5. Altere o âmbito. Se mudar de partilhada único âmbito, só pode selecionar subscrições em que seja proprietário. Apenas as subscrições dentro do contexto de faturação mesmo como a reserva, podem ser selecionadas. O contexto de faturação é determinado pela subscrição que selecionou quando a reserva foi comprou. O âmbito apenas se aplica a subscrições de MS-AZR - 0003P oferta pay as you go e as subscrições de oferta MS-AZR - 0017P empresariais. Para contratos enterprise, subscrições de programador/teste não são elegíveis para obter o desconto de reserva.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma reserva única em duas reservas
 Depois de comprar mais do que uma instância, poderá pretender atribuir instâncias dentro de uma reserva a subscrições diferentes. Por predefinição, todas as instâncias (quantidade especificada durante a compra) tem um âmbito - uma única subscrição ou partilham. Por exemplo, adquirido 10 VMs D2 padrão e especificar o âmbito para ser subscrição A. Agora pretende alterar o âmbito de 7 instâncias de VM reservado para a subscrição A e o restante 3 B. dividir uma reserva de subscrição permite-lhe distribuir as instâncias para a gestão do âmbito granular. Pode simplificar a alocação para subscrições escolhendo âmbito partilhado. Mas para efeitos de gestão ou budgeting de custo, pode atribuir quantidades às subscrições específicas.

 Pode dividir uma reserva para duas reservas apesar do PowerShell, CLI, ou através da API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividir uma reserva utilizando o PowerShell
1. Obter o ID de ordem de reserva, executando o seguinte comando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Obter os detalhes de uma reserva de:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Dividir a reserva para dois e distribuir as instâncias:

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Pode atualizar o âmbito, executando o seguinte comando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Adicionar ou alterar os utilizadores que podem gerir uma reserva
Pode delegar a gestão de uma reserva de adicionando pessoas a funções de reserva. Por predefinição, a pessoa que comprou a reserva e o administrador da conta ter a função de proprietário de reserva. 

Pode gerir o acesso ao reservas independentemente de subscrições que obtenha o desconto de reserva. Quando conceder permissões para gerir uma reserva de alguém, que não conceder-lhes direitos para gerir a subscrição. E se alguém conceder permissões para gerir uma subscrição no âmbito da reserva, que não conceder-lhes direitos para gerir a reserva.
 
Delegar a gestão de acesso para uma reserva de: 
1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).
2.  Selecione **mais serviços** > **reserva** para reservas de lista que tenha acesso.
3.  Selecione a reserva de que pretende delegar o acesso a outros utilizadores.
4.  Selecione **controlo de acesso (IAM)** no menu.
5.  Selecione **adicionar** > **função** > **proprietário** (ou uma função diferente se pretende conceder acesso limitado). 
6. Escreva o endereço de e-mail do utilizador que pretende adicionar como proprietário. 
7. Selecione o utilizador e, em seguida, selecione **guardar**.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda tiver mais perguntas, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
