---
title: "Personalizar regras de firewall de aplicação web no Gateway de aplicação do Azure - portal do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre como personalizar regras de firewall de aplicação do web no Gateway de aplicação com o portal do Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.openlocfilehash: 406e491aa54806b3534ef0f500d6aea03434e2c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Personalizar regras de firewall de aplicação web através do portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [CLI 2.0 do Azure](application-gateway-customize-waf-rules-cli.md)

A firewall de aplicações do Gateway de aplicação do Azure web (WAF) fornece proteção para as aplicações web. Estas proteções são fornecidas pela aplicação do abra Web projeto segurança (OWASP) Core regra definido (CR). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por este motivo, o Gateway de aplicação fornece a capacidade para personalizar grupos de regras e as regras. Para obter mais informações sobre os grupos de regra específica e regras, consulte [lista de grupos de regras de CR de firewall de aplicação de web e regras](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se o gateway de aplicação não estiver a utilizar o escalão de WAF, a opção para atualizar o gateway de aplicação para o escalão de WAF é apresentada no painel direito. 

![Ativar WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Grupos de regras de vista e regras

**Para ver grupos de regras e as regras**
   1. Navegue para o gateway de aplicação e, em seguida, selecione **firewall de aplicações Web**.  
   2. Selecione **configuração da regra avançadas**.  
   Esta vista mostra uma tabela na página de todos os grupos de regra fornecido com o conjunto de regras que escolheu. Todas as caixas de verificação da regra são selecionadas.

![Configurar regras desativadas][1]

## <a name="search-for-rules-to-disable"></a>Procurar regras a desativar

O **Web as definições de firewall de aplicação** painel fornece a capacidade de filtrar as regras através de uma pesquisa de texto. O resultado mostra apenas os grupos de regras e as regras que contêm o texto que pesquisou.

![Procurar regras][2]

## <a name="disable-rule-groups-and-rules"></a>Desativar grupos de regras e as regras

Quando o está a desativar regras, pode desativar um grupo de regras de todo ou regras específicas num ou mais grupos de regra. 

**Para desativar grupos de regra ou regras específicas**

   1. Procure os regras ou grupos de regra que pretende desativar.
   2. Desmarque as caixas de verificação para as regras que pretende desativar. 
   2. Selecione **Guardar**. 

![Guardar alterações][3]

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as regras desativadas, pode saber como ver os registos de WAF. Para obter mais informações, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
