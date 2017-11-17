---
title: "Compreender a utilização de instância reservados do Azure para a sua subscrição pay as you go | Microsoft Docs"
description: "Saiba como ler a utilização da aplicação de compreender de instância reservado para a subscrição pay as you go"
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
ms.openlocfilehash: 6982d6177ef5c94436a28cd68beb9feb5cc343e5
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Compreender a utilização de instância reservado para a sua subscrição pay as you go

Compreender a utilização de instância reservado através de ReservationId de [página reserva](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) e o ficheiro de utilização do [portal de contas do Azure.](https://account.azure.com)


>[!NOTE]
>Este artigo não se aplica aos clientes EA. Se um cliente EA, consulte o artigo [utilização compreender instância reservado para inscrição da sua empresa.](billing-understand-reserved-instance-usage-ea.md) Este artigo também assume que reserva é aplicada a uma única subscrição. Se a reserva é aplicada a mais do que uma subscrição, o benefício de reserva pode abranger vários ficheiros de utilização de csv. 

Para a secção seguinte, partem do princípio de que está a executar uma VM do Windows Standard_DS1_v2 no Leste região EUA e as informações de reserva parece a tabela seguinte:

| Campo | Valor |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantidade |1|
|SKU | Standard_DS1_v2|
|Região | eualeste |

## <a name="reservation-application"></a>Aplicação de reserva

A parte de hardware da VM é abrangida porque a VM implementada corresponda os atributos de reserva. Para ver o software do Windows não está abrangido pela instância reservado, aceda à [os custos de software do Windows de instâncias de VM de reserva do Azure.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Secção de instrução de csv
Esta secção do seu csv mostra a utilização total para a reserva. Aplique o filtro num campo de categoria secundárias medição que contenha "Reserva-" e o parece dados de captura de ecrã seguinte: ![direta de instrução de reserva](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

A linha de Base de reserva de VM tem o número total de horas que estão abrangidas pela reserva. Esta linha é $0.00 porque a instância reservado abrange-lo. Reserva Windows Svr (1 núcleo) linha abrange o custo de software do Windows.

### <a name="daily-usage-section-of-csv"></a>Secção utilização diária de csv
Filtrar as informações adicionais e escreva o ID de reserva. A seguinte captura de ecrã mostra os campos relacionados com a reserva. 

![Custos de utilização diária](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. ReservationId no campo de informações adicionais é a reserva que foi utilizada para aplicar benefício à VM.
2. ConsumptionMeter é o Id de medição para a VM.
3. Linha de Base de reserva VM medição subcategoria representa a linha de custo de $0 na secção de instrução. Custo de execução da VM já é paga pela reserva.
4. Este é o Id de medição de reserva. Custo deste medidor é $0. Qualquer VM que qualificam para a instância reservado tem este MeterId no csv para a conta para o custo. 
5. Standard_DS1_v2 é uma vCPU VM e a VM está implementado sem benefício híbrida do Azure. Por conseguinte, este medidor inclui o custo adicional de software do Windows. Consulte [os custos de software do Windows de instâncias de VM de reserva do Azure.](billing-reserved-instance-windows-software-costs.md) Para localizar o medidor correspondente a série de D 1 núcleo VM. Se for utilizado o benefício de híbrida do Azure, este custo extra não está aplicado. 

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.