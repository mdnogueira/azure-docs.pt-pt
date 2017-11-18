---
title: "Poupar dinheiro pelo previamente pagar para máquinas virtuais do Azure - do Azure | Microsoft Docs"
description: "Saiba mais sobre o Azure instância de Máquina Virtual reservada para guardar os custos de máquinas virtuais."
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
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 96e9cf2fed0b22fd7aa7b9ffeab0e94738ce510d
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Poupar dinheiro em máquinas virtuais com instâncias de Máquina Virtual reservado 
Instâncias de Máquina Virtual reservada permitem-lhe pagar previamente para um ano ou três anos de capacidade de cálculo para obter um desconto nas máquinas virtuais que utiliza. Esta reduz significativamente os custos de máquina virtual — até 72 por cento num preços pay as you go – com o compromisso de compromisso de um ano ou três anos. Instâncias de Máquina Virtual reservada um desconto de faturação e não afeta o estado do tempo de execução de máquinas virtuais.

Pode comprar uma instância de Máquina Virtual reservada [portal do Azure](https://aka.ms/reservations). Para obter mais informações, consulte [Prepay para máquinas virtuais e a poupar dinheiro com instâncias de Máquina Virtual reservada](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>Por que motivo deve a comprar uma instância de Máquina Virtual reservada?
Se tiver máquinas virtuais executadas durante longos períodos de tempo, a compra de uma instância de Máquina Virtual reservada dá-lhe o melhor preço Efetivo. Por exemplo, se executar continuamente quatro instâncias de D2 padrão na região EUA oeste, sem uma reserva de-lhe é cobrados às taxas de pay-como-vá-lhe. Se comprar uma instância de Máquina Virtual reservada os quatro VMs, as VMs obtêm imediatamente o benefício de faturação. Estes são já não debitados às pay-como-lhe acedas taxas. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>Os encargos abranger uma instância de Máquina Virtual reservada?
Uma reserva abrange apenas os custos de infraestrutura de máquina virtual para as máquinas de virtuais do Windows ou Linux. Uma reserva não abrange os encargos de armazenamento, redes ou software adicionais. Para máquinas virtuais do Windows, pode cobrir Windows licenciamento os custos com [Azure híbrida benefício](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>Quem é elegível para comprar uma instância de Máquina Virtual reservada?
Clientes do Azure com estes tipos de subscrições podem comprar uma instância de Máquina Virtual reservado:
-   Enterprise agreement oferta tipo de subscrição (MS-AZR - 0017P).
-   [Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) o tipo de oferta de subscrição (MS-AZR - 003 P).
Tem de ser a função de "Proprietário" na subscrição comprar uma instância reservado. Para a compra de reservas de uma inscrição enterprise, o administrador de empresa tem de ativar compras de reserva no portal do EA, por predefinição, que a definição está ativada.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>Como é faturada uma compra de instâncias de Máquina Virtual reservada?
A compra de reserva é cobrada para o método de pagamento associado à subscrição. Se tiver uma subscrição do Enterprise, o custo de reserva é deducted da sua saldo de compromisso monetário. Se o saldo de compromisso monetário não abrange o custo da reserva, será cobrado o excedido.
Se tiver uma subscrição pay as you go, o cartão de crédito que tem na sua conta é faturado imediatamente. Se será cobrado por fatura, verá os encargos na sua fatura seguinte.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>Como é aplicado o adquiridas desconto de instância de Máquina Virtual reservada?
O desconto de instância de Máquina Virtual reservada aplica-se às máquinas virtuais que correspondam aos atributos que selecionar na compra de reserva. Os atributos incluem o âmbito em que executam as VMs correspondentes. Por exemplo, se pretender um desconto de instância de VM reservado para quatro máquinas virtuais que D2 padrão na região EUA oeste, selecione a subscrição onde as VMs estão em execução. Se estiver a executar as máquinas virtuais em diferentes subscrições na sua conta/inscrição, em seguida, selecione o âmbito como partilhada. Âmbito partilhado permite que o desconto de reserva a aplicar nas subscrições.
Pode alterar o âmbito depois de comprar uma instância de VM reservado. Para alterar o âmbito, consulte a documentação sobre como gerir reservas.

O desconto de reserva só se aplica a máquinas virtuais em subscrições com enterprise ou tipos de oferta pay as you go. Máquinas virtuais em execução numa subscrição com outros tipos de oferta não recebem o desconto de reserva. Para inscrições de empresa, as subscrições de programador/teste empresariais não elegíveis para as vantagens de instância reservado.

Como reserva afeta a faturação da máquina virtual é explicado no [Noções sobre a aplicação de reserva de faturação benefício](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reservation-term-expires"></a>O que acontece quando o termo de reserva expira?
No final do período da reserva, o faturação desconto expira e a infraestrutura de máquina virtual é faturada a pay-como-lhe aceda preço. As reservas não a renovação automática. Para continuar a introdução de desconto de faturação, tem de comprar uma nova instância de Máquina Virtual reservada. 

## <a name="sizes-and-regional-availability"></a>Tamanhos e disponibilidade Regional
As reservas estão disponíveis para a maioria dos tamanhos de VM com algumas exceções:
- Tamanhos de VM de pré-visualização – qualquer dimensão que está em pré-visualização não estão disponíveis para compra de instância de Máquina Virtual reservada.
- Nuvens – reservado instâncias de Máquina Virtual não estão disponíveis para compra nas regiões do Azure US Government, Datacenters ou China. 
- Quota suficiente – A reservado instância de VM que está confinada para uma única subscrição tem de ter vCPU quota disponível na subscrição para o novo RI. Por exemplo, se a subscrição de destino tem um limite de quota de 10 vCPUs para a família de série D, não é possível de comprar uma instância de VM reservado para 11 Standard_D1 instâncias. A verificação de quota de reservas inclui as VMs já implementadas na subscrição. Por exemplo, se a subscrição tem uma quota de 10 vCPUs para a família de série D. Se esta subscrição tem duas instâncias de standard_D1 implementadas, pode comprar uma instância de VM reservado para 10 standard_D1 instâncias nesta subscrição. 
- Restrições de capacidade – circunstâncias raros, compra de limites do Azure de reservas de novo para subconjunto de tamanhos de VM, devido a baixa capacidade numa região.

## <a name="next-steps"></a>Passos seguintes
Início guardar nas suas máquinas virtuais por comprar uma [instância de Máquina Virtual reservada](https://go.microsoft.com/fwlink/?linkid=861721). 

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
