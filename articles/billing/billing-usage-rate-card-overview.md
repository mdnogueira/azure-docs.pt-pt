---
title: "APIs de faturação do Azure | Microsoft Docs"
description: "Saiba mais sobre a utilização de faturação do Azure e RateCard APIs, que são utilizados para fornecer informações de consumo de recursos do Azure e as tendências."
services: 
documentationcenter: 
author: BryanLa
manager: tonguyen
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/9/2017
ms.author: mobandyo;bryanla
ms.openlocfilehash: 26217d6f4e14166a89fbb561cb12d0af78ae6f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Utilize as APIs de faturação do Azure para programaticamente conhecer a utilização do Azure
Utilize as APIs de faturação do Azure para retirar dados de utilização e de recursos para as ferramentas de análise de dados preferencial. A utilização de recursos do Azure e RateCard APIs podem ajudá-lo com precisão prever e gerir os custos. As APIs são implementadas como um fornecedor de recursos e a parte da família de APIs expostas pelo Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>API de transferência da fatura do Azure (pré-visualização)
Uma vez a [optar ativamente por participar no foi concluído](billing-manage-access.md#opt-in), faturas transferir a versão de pré-visualização do [fatura API](/rest/api/billing). As funcionalidades incluem:

* **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com) ou através de [cmdlets Azure PowerShell](/powershell/azure/overview) para especificar quais os utilizadores ou aplicações podem obter acesso à dados de utilização da subscrição. Os chamadores tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função ou o leitor de faturação, leitor, proprietário ou contribuinte obter acesso aos dados de utilização de uma subscrição do Azure específica.
* **Data filtragem** -utilize o `$filter` parâmetro para obter todos os faturas por ordem cronológica inversa, a data de fim do período da fatura. 

> [!NOTE]
> Esta funcionalidade é a primeira versão de pré-visualização e poderão estar sujeitas a alterações incompatíveis com versões anteriores. Atualmente, não é disponível para determinados ofertas de subscrição (EA CSP, AIO não suportada) e os Datacenters do Azure.

## <a name="azure-resource-usage-api-preview"></a>Utilização de recursos do Azure API (pré-visualização)
Utilizar o Azure [API de utilização de recursos](https://msdn.microsoft.com/library/azure/mt219003) para obter os seus dados de consumo estimado do Azure. A API inclui:

* **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com) ou através de [cmdlets Azure PowerShell](/powershell/azure/overview) para especificar quais os utilizadores ou aplicações podem obter acesso à dados de utilização da subscrição. Os chamadores tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função ou o leitor de faturação, leitor, proprietário ou contribuinte obter acesso aos dados de utilização de uma subscrição do Azure específica.
* **Hora a hora ou agregações diárias** - os chamadores podem especificar registos se pretendem os seus dados de utilização do Azure hora a hora ou diária de registos. A predefinição é diária.
* **Os metadados de instância (inclui os sinalizadores de recurso)** – obter detalhes de nível de instância, como o uri de recurso completamente qualificado (/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} /..), a informações do grupo de recursos e as etiquetas de recursos. Estes metadados ajuda-o a deterministicamente e através de programação alocar utilização por etiquetas, para casos de utilização como charging de cruzada.
* **Metadados do recurso** -detalhes de recursos, tais como o nome de medição, categoria de medição, categoria de subchaves de medição, unidade e região dar o autor da chamada uma melhor compreensão sobre o que estava a ser consumido. Estamos a trabalhar também para assegurar a terminologia de metadados de recursos através do portal do Azure, a utilização do Azure CSV, EA CSV e outras experiências de destinado ao público, permitem-lhe correlacionar dados através de experiências de faturação.
* **Utilização de tipos de oferta diferentes** – os dados de utilização estão disponíveis para os tipos de oferta pay as you go, MSDN, compromisso monetário, crédito monetário e EA, exceto [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Recursos do Azure RateCard API (pré-visualização)
Utilize o [API do Azure Resource RateCard](https://msdn.microsoft.com/library/azure/mt219005) para obter a lista de recursos do Azure disponíveis e as informações de preços estimadas para cada. A API inclui:

* **Controlo de acesso baseado em funções do Azure** -configurar as políticas de acesso no [portal do Azure](https://portal.azure.com) ou através de [cmdlets Azure PowerShell](/powershell/azure/overview) para especificar quais os utilizadores ou aplicações podem obter acesso a os dados de RateCard. Os chamadores tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função ou o leitor, proprietário ou contribuinte obter acesso aos dados de utilização para uma determinada subscrição do Azure.
* **Suporte para pay as you go, MSDN, compromisso monetário e ofertas de crédito monetário (EA e [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) não suportada)** -esta API fornece informações de taxa de nível de oferta do Azure.  O autor da chamada desta API tem de passar as informações de oferta para obter os detalhes do recurso e taxas. Estamos atualmente não é possível fornecer taxas EA porque EA ofertas tem personalizado taxas por inscrição. 

## <a name="scenarios"></a>Cenários
Seguem-se alguns dos cenários que são efetuados possíveis com a combinação da utilização e as APIs de RateCard:

* **Azure gastam durante o mês** - utilize a combinação de padrões de utilização e gaste RateCard APIs para obter informações sobre a melhor nuvem durante o mês. Pode analisar os registos de hora a hora e diários de estimativas de utilização e encargos.
* **Configure alertas** – utilizar a utilização e as APIs de RateCard para obter o consumo de nuvem estimado e custos e configure baseada em recursos ou monetário com base em alertas.
* **Prever fatura** – Get seu consumo estimado nuvem gastam e aplicar algoritmos de machine learning para prever a que a fatura seria no fim do ciclo de faturação.
* **Pré-consumo de análise de custos** – utilizar a API de RateCard para prever a quantidade a fatura seria para a sua utilização esperada ao mover as cargas de trabalho para o Azure. Se tiver cargas de trabalho existentes em nuvens privadas ou outros nuvens, também é possível mapear a utilização com o Azure gastam a taxas para obter uma estimativa melhor do Azure. Esta estimativa dá-lhe a capacidade de dinâmico na oferta e comparar e contraste entre os tipos de oferta diferentes para além de pay as you go, como o compromisso monetário e crédito monetário. Além disso, a API dá-lhe a capacidade para ver as diferenças de custo por região e permite-lhe fazer uma análise de custos investiguem para o ajudar a tomar decisões de implementação.
* **Análise de investiguem** -
  
  * Pode determinar se é mais económico executar cargas de trabalho noutra região, ou outra configuração do recurso do Azure. Os custos de recursos do Azure podem divergir com base na região do Azure que está a utilizar.
  * Também pode determinar se o outro tipo de oferta do Azure fornece uma melhor taxa sobre um recurso do Azure.
  
## <a name="partner-solutions"></a>Soluções de parceiros
[Nuvem Cruiser e a integração de API de faturação do Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) descreve como [rápida da nuvem Cruiser para Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) funciona diretamente a partir do portal do Windows Azure Pack (WAP). Totalmente integrada pode gerir aspetos operacionais e financeiros do Microsoft Azure alojada ou privada nuvem pública a partir de uma interface de utilizador único.   

## <a name="next-steps"></a>Passos seguintes
* Consulte os exemplos de código no GitHub:
  * [Exemplo de código da fatura API](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Exemplo de código da API de utilização](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Exemplo de código da API de RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Para saber mais sobre o Azure Resource Manager, consulte [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

* Para mais informações sobre o conjunto de ferramentas necessárias para o ajudar a obter uma compreensão dos cloud gastam, consulte o artigo Gartner [guia mercado para ferramentas de gestão financeiros TI (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

