---
title: "Pesquisa de centro de segurança do Azure | Microsoft Docs"
description: "Saiba como o Centro de segurança do Azure utiliza a pesquisa de análise de registos para obter e analisar os dados de segurança."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-search"></a>Pesquisa de centro de segurança do Azure
Centro de segurança do Azure utiliza [análise de registos de pesquisa](../log-analytics/log-analytics-log-searches.md) para obter e analisar os dados de segurança. Análise de registos inclui uma linguagem de consulta para obter e consolidar os dados rapidamente. Do Centro de segurança, pode tirar partido da análise de registos de pesquisa para criar consultas e analisar os dados recolhidos.

Pesquisa está disponível no escalão gratuito e escalão Standard do Centro de segurança.  Os dados disponíveis na sua pesquisas de registo estão dependentes de nível da camada aplicado à sua área de trabalho.  Consulte o Centro de segurança [página de preços](../security-center/security-center-pricing.md) para obter mais informações.


> [!NOTE]
> Centro de segurança não guardar os dados de segurança para uma área de trabalho em escalão gratuito. Pode enviar uma variedade de registos para uma área de trabalho sob o escalão gratuito e pesquisa nesses dados, mas os resultados da procura não incluem dados a partir do Centro de segurança. Centro de segurança só poupa dados para uma área de trabalho com o escalão Standard.
>
>

## <a name="access-search"></a>Pesquisa de acesso
1. No menu principal do Centro de segurança, selecione **pesquisa**.

  ![Selecione a pesquisa de registo][1]

2. Centro de segurança apresenta uma lista de todas as áreas de trabalho nas suas subscrições do Azure. Selecione uma área de trabalho. (Se tiver apenas uma área de trabalho, este Seletor de área de trabalho não são apresentados.)

  ![Selecione uma área de trabalho][2]

3. **Registo de pesquisa** abre. Para consultar mais dados na área de trabalho selecionado, introduza esta consulta de exemplo:

  SecurityEvent | onde EventID = = 4625 | resumir existente pelo TargetAccount

  Resultado mostra todas as contas que falharam ao início de sessão (evento 4625).

  ![Resultados da pesquisa][3]

Consulte [idioma de consulta de análise de registos](../log-analytics/log-analytics-search-reference.md) para obter mais informações sobre a consulta de dados na área de trabalho selecionado.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu como aceder a pesquisa no Centro de segurança. Centro de segurança utiliza a pesquisa de análise de registos. Para saber mais sobre a pesquisa de análise de registos, consulte:

- [O que é o Log Analytics?](../log-analytics/log-analytics-overview.md) – Descrição geral na análise de registos
- [Registo de compreender procura na análise de registos](../log-analytics/log-analytics-log-search-new.md) - descreve como as pesquisas de registo são utilizadas na análise de registos e fornece os conceitos que devem ser compreendidos antes de criar uma pesquisa de registo
- [Localizar dados através de pesquisas de registo na análise de registos](../log-analytics/log-analytics-log-searches.md) – Tutorial sobre como utilizar a pesquisa de registo
- [Análise de registos de pesquisa referência](../log-analytics/log-analytics-search-reference.md) – descreve o idioma de consulta na análise de registos

Para saber mais acerca do Centro de segurança, consulte:

- [Descrição geral do Centro de segurança](security-center-intro.md) – principais capacidades de centro de segurança a Describes

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
