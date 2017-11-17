---
title: "Compreender a utilização de instância reservados do Azure para Enterprise | Microsoft Docs"
description: "Saiba como ler a sua utilização para compreender a aplicação de instância reservado para inscrição da sua empresa."
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 7ef601033b36ee968cb766d40a0a6b05afa9a1a4
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>Compreender a utilização de instância reservado para a inscrição Enterprise
Compreender a utilização de instância reservado através de ReservationId de [página reserva](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) e o ficheiro de utilização do [EA portal.](https://ea.azure.com) Também pode ver a utilização de reserva na secção de resumo de utilização do [EA portal.](https://ea.azure.com)

>[!NOTE]
>Se comprou a reserva num contexto de faturação pay as you go, consulte o artigo [utilização compreender instância reservado para a sua subscrição pay as you go.](billing-understand-reserved-instance-usage.md)

Para a secção seguinte, partem do princípio de que está a executar uma VM do Windows Standard_D1_v2 no Leste região EUA e as informações de reserva parece a tabela seguinte:

| Campo | Valor |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantidade |1|
|SKU | Standard_D1|
|Região | eualeste |

## <a name="reservation-application"></a>Aplicação de reserva

A parte de hardware da VM é abrangida porque a VM implementada corresponda os atributos de reserva. Para ver o software do Windows não está abrangido pela instância reservado, aceda a custos de software reservado instâncias de VM do Azure, aceda a [os custos de software do Windows de instâncias de VM de reserva do Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reservation-usage-in-csv"></a>Utilização de reserva no csv
Pode transferir o csv de utilização EA partir do portal EA. No ficheiro csv transferido, filtrar as informações adicionais e escreva o ID de reserva. A seguinte captura de ecrã mostra os campos relacionados com a reserva:

![EA csv para a instância reservado](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. ReservationId no campo de informações adicionais representa a reserva que foi utilizada para aplicar benefício à VM.
2. ConsumptionMeter é MeterId para a VM.
3. Este é ReservationMeter implica um custo $0, desde que o custo de execução da VM já é paga pela reserva. 
4. Standard_D1 é uma vCPU VM e a VM está implementado sem benefício híbrida do Azure. Por conseguinte, este medidor inclui o custo adicional de software do Windows. Consulte [os custos de software do Windows de instâncias de VM de reserva do Azure.](billing-reserved-instance-windows-software-costs.md) Para localizar o medidor correspondente a série de D 1 núcleo VM. Se for utilizado o benefício de híbrida do Azure, este custo extra não será aplicado.

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>Utilização de reserva na página de resumo de utilização no portal EA

Reservado instância utilização também aparece na secção de resumo de utilização do portal EA: ![EA este resumo de utilização](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Não são cobradas para o componente de hardware da VM, tal como está abrangido por instância reservado. 
2. São-lhe cobrados de software do Windows que benefício híbrida do Azure não está a ser utilizado. 

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.