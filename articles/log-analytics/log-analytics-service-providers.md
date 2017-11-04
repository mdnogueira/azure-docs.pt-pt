---
title: "Funcionalidades de análise de registo para fornecedores de serviços | Microsoft Docs"
description: "Análise de registos pode ajudar geridos fornecedores de serviços (MSPs), as grandes empresas, os fabricantes Sofware independentes (ISVs) e fornecedores de serviços de alojamento gerirem e monitorizar os servidores de infraestrutura de nuvem ou no local do cliente."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 1a8bc500c837d60b4106f37f1803415a12b62a88
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="log-analytics-features-for-service-providers"></a>Funcionalidades de análise do registo para fornecedores de serviços
Análise de registos pode ajudar os fornecedores de serviços geridos (MSPs), as grandes empresas, os fabricantes independentes de software (ISV) e fornecedores de serviços de alojamento gerir e monitorizar os servidores de infraestrutura de nuvem ou no local do cliente. 

As grandes empresas partilham diversas semelhanças com fornecedores de serviços, especialmente quando existe uma equipa de TI centralizada que é responsável pela gestão IT para muitas unidades empresariais diferentes. Simplicidade, este documento utiliza o termo *fornecedor de serviços* , mas a mesma funcionalidade também está disponível para as empresas e outros clientes.

## <a name="cloud-solution-provider"></a>Fornecedor de Soluções Cloud
Parceiros e fornecedores de serviço que fazem parte do [fornecedor de solução em nuvem (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) programa, análise de registos é um dos serviços do Azure disponíveis no [subscrição Azure CSP](https://docs.microsoft.com/en-us/azure/cloud-solution-provider/overview/azure-csp-overview). 

Para análise de registos, as seguintes funcionalidades estão ativadas em *provedor de soluções de nuvem* subscrições.

Como um *provedor de soluções de nuvem* , pode:

* Crie áreas de trabalho de análise de registos na subscrição de um inquilino (cliente).
* Áreas de trabalho de acesso criadas pelos inquilinos. 
* Adicionar e remover utilizador acesso à área de trabalho utilizando a gestão de utilizadores do Azure. Quando na área de trabalho de um inquilino no portal do OMS, a gestão de utilizadores página em definições não está disponível
  * Análise de registos não suporta o acesso baseado em funções ainda - dar um utilizador `reader` permissão no portal do Azure permite-los efetuar alterações de configuração no portal do OMS

Para iniciar sessão subscrição do inquilino, tem de especificar o identificador de inquilino. O identificador de inquilino é muitas vezes, essa última parte do endereço de correio electrónico utilizado para iniciar sessão.

* No portal do OMS, adicionar `?tenant=contoso.com` no URL para o portal. Por exemplo, `mms.microsoft.com/?tenant=contoso.com`
* No PowerShell, utilize o `-Tenant contoso.com` parâmetro ao utilizar `Add-AzureRmAccount` cmdlet
* O identificador de inquilino é adicionado automaticamente ao utilizar o `OMS portal` ligação a partir do portal do Azure para abra e inicie sessão portal do OMS para a área de trabalho selecionado

Como um *cliente* de um fornecedor de solução em nuvem, pode:

* Criar registo de áreas de trabalho de análise numa subscrição CSP
* Áreas de trabalho de acesso criadas pelo CSP
  * Utilize o `OMS portal` ligação a partir do portal do Azure para abra e inicie sessão portal do OMS para a área de trabalho selecionado
* Ver e utilizar a página de gestão de utilizadores em definições no portal do OMS

> [!NOTE]
> As soluções de cópia de segurança e recuperação de sites incluídas para análise de registos não são possível estabelecer ligação com um cofre dos serviços de recuperação e não podem ser configuradas numa subscrição do CSP. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Gerir vários clientes através da análise do registo
Recomenda-se que crie uma área de trabalho de análise de registos de cada cliente, que gere. Fornece uma área de trabalho de análise de registos:

* Uma localização geográfica para os dados armazenados. 
* Granularidade para faturação 
* Isolamento de dados 
* Configuração exclusiva

Ao criar uma área de trabalho por cliente, que é possível manter os dados de cada cliente separados e também controlar a utilização de cada cliente.

Obter mais detalhes sobre quando e Porquê criar várias áreas de trabalho está descrita em [gerir o acesso a análise de registo](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Criação e configuração de áreas de trabalho do cliente podem ser automatizadas utilizando [PowerShell](log-analytics-powershell-workspace-configuration.md), [modelos do Resource Manager](log-analytics-template-workspace-configuration.md), ou utilizando o [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

A utilização de modelos do Resource Manager para a configuração de área de trabalho permite-lhe ter uma configuração principal que pode ser utilizada para criar e configurar áreas de trabalho. Pode ter a certeza de que como áreas de trabalho são criadas para os clientes são automaticamente configurados para os seus requisitos. Quando atualizar os seus requisitos, o modelo é atualizado e, em seguida, reaplicada as áreas de trabalho existentes. Este processo garante que o mesmo existentes áreas de trabalho cumpram as normas de novo.    

Quando gerir várias áreas de trabalho de análise de registos, recomendamos a integração de cada área de trabalho com o seu sistema de emissão de permissões existente / utilizando a consola de operações a [alertas](log-analytics-alerts.md) funcionalidade. Através da integração com os sistemas existentes, pessoal de suporte pode continuar a seguir os processos familiares. Análise de registos regularmente verifica cada área de trabalho contra os critérios de alerta que especificar e gera um alerta quando for necessária ação.

Para personalizado vistas de dados, utilize o [dashboard](../azure-portal/azure-portal-dashboards.md) capacidade no portal do Azure.  

Para os relatórios de nível executivos resumir os dados em áreas de trabalho pode utilizar a integração entre o análise de registos e [PowerBI](log-analytics-powerbi.md). Se precisar de integrar com outro sistema de relatórios, pode utilizar a API de pesquisa (através do PowerShell ou [REST](log-analytics-log-search-api.md)) para executar consultas e exportar os resultados da pesquisa.

## <a name="next-steps"></a>Passos Seguintes
* Automatizar a criação e a configuração de áreas de trabalho utilizando [modelos do Resource Manager](log-analytics-template-workspace-configuration.md)
* Automatizar a criação de áreas de trabalho utilizando [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Utilize [alertas](log-analytics-alerts.md) para integrar com sistemas existentes
* Gerar relatórios de resumo utilizando [PowerBI](log-analytics-powerbi.md)

