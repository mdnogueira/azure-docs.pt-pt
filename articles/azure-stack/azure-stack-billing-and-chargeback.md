---
title: "Faturação de cliente e o estorno na pilha do Azure | Microsoft Docs"
description: "Saiba como obter as informações de utilização de recursos do Azure pilha."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Utilização e faturação na pilha do Azure

Utilização representa a quantidade de recursos que são consumidos por um utilizador. Pilha do Azure recolhe informações de utilização para cada utilizador e utiliza-a bem ao faturá-los. Este artigo descreve como os utilizadores de pilha do Azure são cobrados para utilização de recursos e como as informações de faturação são acedidas para análise, os encargos, etc.

Pilha do Azure contém a infraestrutura para recolher e agregar dados de utilização para todos os recursos e reencaminhar estes dados para comércio do Azure. Pode aceder a estes dados e exportá-lo a um sistema de Faturação através da utilização de um adaptador de faturação ou exportá-los para uma ferramenta de business intelligence como o Microsoft Power BI. Depois de exportar, estas informações de faturação são utilizadas para análise ou ser transferidas para um sistema de estorno.

![Modelo concetual de um adaptador de faturação ligar pilha do Azure para uma faturação aplicação](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>Pipeline de utilização

Cada fornecedor de recursos na pilha do Azure emite dados de utilização de acordo com a utilização de recursos. O serviço de utilização periodicamente (hora a hora ou diariamente) agrega estes dados de utilização e armazena-os na base de dados de utilização. Os dados de utilização armazenados podem ser acedidos por operadores de pilha do Azure e os utilizadores localmente ao utilizar as APIs de utilização. 

Se tiver [registado a sua instância de pilha do Azure com o Azure](azure-stack-register.md), Bridge de utilização está configurado para enviar os dados de utilização para comércio do Azure. Depois dos dados estão disponíveis no Azure, pode aceder através do portal de faturação ou através da API de utilização do Azure. Consulte o [relatórios de dados de utilização](azure-stack-usage-reporting.md) tópico para saber mais sobre a utilização de que dados são reportados para o Azure. 

A imagem seguinte mostra os componentes chave no pipeline de utilização:

![Pipeline de utilização](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>As informações de utilização posso encontrar e como?

Fornecedores de recursos de pilha do Azure, tais como a computação, armazenamento e rede, geram dados de utilização em intervalos por hora para cada subscrição. Os dados de utilização contém informações sobre o recurso utilizado como nome de recurso, a subscrição utilizada, a quantidade utilizado, etc. Para saber mais sobre os recursos de ID de medidores, consulte o [utilização API FAQ](azure-stack-usage-related-faq.md) artigo. 

Depois dos dados de utilização tenham sido recolhidos, é [reportado para o Azure](azure-stack-usage-reporting.md) para gerar uma fatura, que pode ser visualizada através do portal de faturação do Azure. 

> [!NOTE]
> Relatórios de dados de utilização não é necessário para o Kit de desenvolvimento de pilha do Azure e para os utilizadores de sistema de pilha do Azure integrado que licença no modelo de capacidade. Para saber mais sobre o licenciamento na pilha do Azure, consulte o [empacotamento e preços](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) folha de dados.

O portal de faturação do Azure mostra os dados de utilização apenas para os recursos cobráveis. Para além dos recursos de cobráveis, a pilha do Azure captura dados de utilização para um conjunto alargado de recursos, o qual pode aceder no seu ambiente de pilha do Azure através de REST APIs ou PowerShell. Os operadores do Azure da pilha podem obter os dados de utilização para todas as subscrições de utilizador, enquanto que um utilizador pode obter apenas os detalhes de utilização.

## <a name="retrieve-usage-information"></a>Obter informações de utilização

Para gerar os dados de utilização, deverá ter recursos que estão em execução e ativamente utilizando o sistema, por exemplo, uma máquina virtual ativa ou uma conta de armazenamento que contém alguns dados, etc. Se não tiver a certeza se tem quaisquer recursos em execução no Azure Marketplace da pilha, implementar uma máquina virtual (VM) e certifique-se a VM monitorização do painel para se certificar de está em execução. Utilize os seguintes cmdlets PowerShell para ver os dados de utilização:

1. [Instale o PowerShell para a pilha do Azure.](azure-stack-powershell-install.md)
2. [Configurar o utilizador do Azure pilha](user/azure-stack-powershell-configure-user.md) ou [do operador de pilha do Azure](azure-stack-powershell-configure-admin.md) ambiente de PowerShell 

3. Para obter os dados de utilização, utilize o [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) cmdlet do PowerShell:

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>Passos seguintes

[Reportar dados de utilização de pilha do Azure para o Azure](azure-stack-usage-reporting.md)

[API de utilização de recursos do fornecedor](azure-stack-provider-resource-api.md)

[API de utilização de recursos de inquilino](azure-stack-tenant-resource-usage-api.md)

[FAQ relacionados com a utilização](azure-stack-usage-related-faq.md)

