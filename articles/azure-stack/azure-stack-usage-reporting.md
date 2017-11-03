---
title: "Reportar dados de utilização de pilha do Azure para o Azure | Microsoft Docs"
description: "Saiba como configurar dados de utilização de relatórios na pilha do Azure."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: sngun;AlfredoPizzirani
ms.openlocfilehash: 5abc325a6e7c019dc3cb84f7f6ff63c3eb2ff76c
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Reportar dados de utilização de pilha do Azure para o Azure 

Dados de utilização, também designado por dados de consumo representa a quantidade de recursos utilizados. 

Azure sistemas de vários nós pilha que utilizem o modelo de faturação baseada em consumo devem reportar dados de utilização do Azure para fins de faturação.  Os operadores do Azure da pilha devem configurar as respetivas instâncias de pilha do Azure para dados de utilização de relatórios para o Azure.

> [!NOTE]
> Relatórios de dados de utilização é necessário para os pilha do Azure com vários nós utilizadores no modelo de pagamento-como-,-utilização de licenças. É opcional para os clientes que licença no modelo de capacidade (consulte o [como comprar o página](https://azure.microsoft.com/overview/azure-stack/how-to-buy/ to learn more about pricing in Azure Stack)). Para os utilizadores do Kit de desenvolvimento de pilha do Azure, operadores de pilha do Azure podem reportar dados de utilização e a funcionalidade de teste. No entanto, os utilizadores não serão cobrados por qualquer utilização que implicar. 


![fluxo de faturação](media/azure-stack-usage-reporting/billing-flow.png)

Dados de utilização são enviados da pilha do Azure para o Azure através de Bridge de Azure. No Azure, o sistema de comércio processa os dados de utilização e gera a fatura. Depois da fatura é gerada, o proprietário da subscrição do Azure pode ver e transferi-lo do [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions). Para saber mais sobre como a pilha do Azure é licenciada, consulte o [Azure pilha empacotamento e preços documento](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Configurar relatórios de dados de utilização

Para configurar relatórios de dados de utilização, terá [registar a instância de pilha do Azure com o Azure](azure-stack-register.md). Como parte do processo de registo, o componente de Bridge de Azure da pilha do Azure, que se liga a pilha do Azure para o Azure e envia os dados de utilização, está configurado. Os seguintes dados de utilização são enviados de pilha do Azure para o Azure:

- **ID de medidor** – ID exclusivo para o recurso que foi utilizado.
- **Quantidade** – quantidade de utilização de recursos.
- **Localização** – localização em que é implementado o recurso de pilha do Azure atual.
- **URI do recurso** – totalmente qualificado URI do recurso para o qual está a ser reportada utilização.
- **ID de subscrição** – ID de subscrição de utilizador a pilha do Azure. Esta é a subscrição (pilha do Azure) local.
- **Tempo** – hora de início e de fim dos dados de utilização. Não há algum atraso entre a hora quando a estes recursos estejam consumidos na pilha do Azure e os dados de utilização são considerados comércio. Azure dados de utilização de agregados de pilha para a cada 24 horas e os dados de utilização de relatórios para o pipeline de comércio no Azure demora algumas horas outro. Por isso, utilização que ocorre pouco tempo antes de à meia-noite pode aparecer no Azure ao dia seguinte.

## <a name="generate-usage-data-reporting"></a>Gerar relatórios de dados de utilização

1. Para testar os relatórios de dados de utilização, crie alguns recursos na pilha do Azure. Por exemplo, pode criar um [conta de armazenamento](azure-stack-provision-storage-account.md), [VM do Windows Server](azure-stack-provision-vm.md) e uma VM com Linux com básico e padrão de SKUs para ver como utilização principal é comunicada. Os dados de utilização para diferentes tipos de recursos são reportados como medidores diferentes.

2. Deixe os recursos em execução para algumas horas. Informações de utilização são recolhidas, aproximadamente, uma vez a cada hora. Depois de recolher, estes dados são transmitidos para o Azure e processados para o sistema de comércio do Azure. Este processo pode demorar algumas horas.

## <a name="view-usage---csp-subscriptions"></a>Ver utilização - subscrições de CSP

Se registou a pilha do Azure com uma subscrição do CSP, pode ver a sua utilização e os eventuais encargos da mesma forma na qual pode ver o consumo do Azure. Utilização de pilha do Azure será incluída na sua fatura e no ficheiro de reconciliação, disponível através de [Centro de parceiros](https://partnercenter.microsoft.com/en-us/partner/home). O ficheiro de reconciliação é atualizado mensalmente. Se precisar de aceder às informações de utilização recentes pilha do Azure, pode utilizar as APIs do Centro de parceiros.

   ![Centro de parceiros](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Ver utilização – subscrições Enterprise Agreement

Se registou a pilha do Azure com uma subscrição do Enterprise Agreement, pode ver a sua utilização e eventuais encargos no [EA Portal](https://ea.azure.com/). Utilização de pilha do Azure será incluída nas transferências avançadas, juntamente com a utilização do Azure na secção dos relatórios no portal do EA. 

## <a name="view-usage--other-subscriptions"></a>Ver utilização – outras subscrições

Se registou a pilha do Azure através de qualquer outro tipo de subscrição, por exemplo, uma subscrição Pay As You Go, pode ver e custos de utilização no Centro de conta do Azure. Iniciar sessão para o [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions) como o Azure administrador de conta e selecione a subscrição do Azure que utilizou para registar a pilha do Azure. Pode ver os dados de utilização de pilha do Azure, a quantidade cobrada para cada um dos recursos utilizados, tal como mostrado na imagem seguinte:

   ![fluxo de faturação](media/azure-stack-usage-reporting/pricing-details.png)

Para o Kit de desenvolvimento de pilha do Azure, não serem cobrados os recursos de pilha do Azure, pelo que, o preço é mostrado como $0.00. Quando vários nós de pilha do Azure torna-se geralmente disponível, pode ver o custo real para cada um destes recursos.

## <a name="which-azure-stack-deployments-are-charged"></a>As implementações de pilha do Azure são-lhe cobradas?

Utilização de recursos é gratuita em Kit de desenvolvimento de pilha do Azure. Enquanto que para os sistemas de vários nós de pilha do Azure, a carga de trabalho VMs, serviços de armazenamento e serviços de aplicação são-lhe cobrados.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Os utilizadores serem cobrados para a infraestrutura de VMs?

Não. Dados de utilização para algumas fornecedor de recursos do Azure pilha VMs são reportados para o Azure, mas existem não existem custos para estas VMs, nem para as VMs criadas durante a implementação ativar a infraestrutura de pilha do Azure.  

Os utilizadores são-lhe cobrados apenas para as VMs que são executados com subscrições de inquilino. Todas as cargas de trabalho tem de ser implementadas em subscrições de inquilino para estar em conformidade com os termos de licenciamento da pilha do Azure.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Tenho uma licença do Windows Server que pretendo utilizar na pilha do Azure, como fazê-lo?

Utilização de licenças existentes evita a gerar medidores de utilização. Licenças existentes do Windows Server podem ser utilizadas na pilha do Azure, conforme descrito na secção «Utilizar software existente com a pilha do Azure» [guia de licenciamento do Azure pilha](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Os clientes tem de implementar as suas máquinas virtuais do Windows Server, conforme descrito no [benefício híbrida licença do Windows Server](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing) tópico para poder utilizar as respetivas licenças existentes.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>A subscrição é-lhe cobrada os recursos consumidos?
A subscrição que é fornecida quando [registar pilha do Azure com o Azure](azure-stack-register.md) é cobrada.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Que tipos de subscrições são suportados para relatórios de dados de utilização?

Para multinode de pilha do Azure, subscrições de CSP e Enterprise Agreement (EA) são suportadas. Para o Kit de desenvolvimento de pilha do Azure, o Enterprise Agreement (EA), pay as you go, CSP e MSDN subscrições suportam relatórios de dados de utilização.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>É o trabalho em nuvens sovereign os relatórios de dados de utilização?

No Kit de desenvolvimento de pilha do Azure, relatórios de dados de utilização requer subscrições que são criadas no sistema global do Azure. Não não possível registar as subscrições criadas das nuvens sovereign (Azure Government, Datacenters do Azure e Azure China nuvens) com o Azure, pelo que não suportam relatórios de dados de utilização.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Como é que os utilizadores podem identificar os dados de utilização de pilha do Azure no portal de faturação do Azure?

Os utilizadores podem ver os dados de utilização de pilha do Azure no ficheiro de detalhes de utilização. Para saber sobre como obter o ficheiro de detalhes de utilização, consulte o [transferir ficheiros de utilização do artigo do Centro de contas do Azure](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). O ficheiro de detalhes de utilização contém medidores a pilha do Azure que identificam o armazenamento de pilha do Azure e VMs. Todos os recursos utilizados na pilha do Azure são reportados como a região com o nome "Pilha do Azure".

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Por que razão a utilização comunicada na pilha do Azure não coincide com o relatório gerado a partir do Centro de contas do Azure?

É sempre um delaybetween os dados de utilização comunicados pela utilização de pilha do Azure APIs e os dados de utilização comunicados pelo centro de contas do Azure... Este atraso é o tempo necessário para carregar dados de utilização de pilha do Azure para comércio do Azure. Devido a este atraso, utilização que ocorre pouco tempo antes de à meia-noite pode aparecer no Azure ao dia seguinte. Se utilizar o [APIs de utilização do Azure pilha](azure-stack-provider-resource-api.md)e comparar os resultados para a utilização comunicados no portal de faturação do Azure, pode ver uma diferença.

## <a name="next-steps"></a>Passos seguintes

* [API de utilização do fornecedor](azure-stack-provider-resource-api.md)  
* [API de utilização do inquilino](azure-stack-tenant-resource-usage-api.md)
* [FAQ de utilização](azure-stack-usage-related-faq.md)
