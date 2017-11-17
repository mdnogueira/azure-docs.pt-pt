---
title: "Compreender a aplicação de desconto reservado instâncias de Máquina Virtual do Azure | Microsoft Docs"
description: "Saiba como desconto reservado instância de VM do Azure é aplicado para VMs em execução."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: 3fd12bd3c51eeef57c896da030a83e447dc3e8ff
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>Compreender a forma como é aplicado o desconto de instância de Máquina Virtual reservado
Depois de comprar uma instância de VM reservado, o desconto de reserva é aplicado automaticamente a máquinas virtuais, os atributos e a quantidade da reserva de correspondência. Uma reserva abrange os custos de infraestrutura das suas máquinas virtuais. A tabela seguinte ilustra os custos para a máquina virtual depois de comprar uma reserva. Em todos os casos, são-lhe cobrados para armazenamento e redes nas taxas normais.

| Tipo de máquina virtual  | Custos de reserva |    
|-----------------------|--------------------------------------------| 
|VMs do Linux sem software adicional | Reserva abrange os custos de infraestrutura VM.|
|VMs com Linux com custos de software (por exemplo, Red Hat) | Reserva abrange os custos de infraestrutura. São-lhe cobrados para o software adicional.|
|VMs do Windows sem software adicional |Reserva abrange os custos de infraestrutura. São-lhe cobrados de software do Windows.|
|VMs do Windows com o software adicional (por exemplo, o SQL server) | Reserva abrange os custos de infraestrutura. São-lhe cobrados para software do Windows e para software adicional.|
|VMs do Windows com [benefício híbridos do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Reserva abrange os custos de infraestrutura. Os custos de software do Windows estão abrangidos pelo benefício de híbrida do Azure. Qualquer software adicional é cobrada separadamente.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Aplicação de desconto de reserva para não - VMs do Windows
 O desconto de reserva é aplicado a executar instâncias VM numa base horária. As reservas que que comprou são correspondidas para utilização emitida pelas VMs em execução para aplicar o desconto de reserva. O gráfico seguinte ilustra a aplicação de uma reserva para utilização VM sujeito a faturação. A ilustração é com base na compra de reserva de uma e duas instâncias VM correspondentes.

![Aplicação de instância de VM reservada](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Qualquer utilização que é superior a linha de instância de VM reservado obtém debitada às taxas de pay as you go regulares. Não está encargos para qualquer utilização abaixo linha reservado instâncias de VM, uma vez que tenha sido já paga como parte da compra de reserva.
2.  Na hora 1, instância 1 executa 0.75 horas e instância 2 executa 0.5 horas. Total de utilizações para hora 1 é 1.25 horas. São-lhe cobrados a taxas pay as you go para as horas 0,25 restantes.
3.  Para hora 2 e 3 de hora, ambas as instâncias foi executada para 1 hora. Uma instância é abrangida pela reserva e o outro é debitado às taxas de pay as you go.
4.  Para as 4 horas, instância 1 executa 0.5 horas e instância 2 executa para 1 hora. Instância 1 totalmente é abrangida pela reserva e são explicadas 0.5 horas de instância de 2. Está a cobrada a taxa de pay as you go para as horas 0.5 restantes.

Compreender e ver a aplicação do seu reservas relatórios de utilização de faturação, consulte [utilização compreender reservado instância de VM](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Aplicação de desconto de reserva para VMs do Windows
Quando estiver a executar instâncias de VM do Windows, é aplicada a reserva para cobrir os custos de infraestrutura. A aplicação de reserva para os custos de infraestrutura VM para VMs do Windows é o mesmo para não - VMs do Windows. São-lhe cobrados em separado para software do Windows numa base por vCPU. Consulte [os custos de software do Windows com reservas](https://go.microsoft.com/fwlink/?linkid=862756). Pode abranger o Windows os custos com [Azure híbrida benefício para o Windows Server] (https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing) de licenciamento.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
