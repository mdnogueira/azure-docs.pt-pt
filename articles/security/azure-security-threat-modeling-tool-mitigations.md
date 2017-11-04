---
title: "Azure mitigações - ferramenta de modelação de ameaça Microsoft - | Microsoft Docs"
description: "Página de mitigações para a Microsoft ameaça modelação ferramenta Realce possíveis soluções para o mais expostas gerado ameaças."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 07ef1fd3d81d795c9164741d22b5a689f86bd720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Mitigações de ferramenta de modelação de ameaça na Microsoft

A ferramenta de modelação de ameaça é um elemento principal do Microsoft Security Development Lifecycle (SDL). Permite arquitetos de software identificar e mitigar os potenciais problemas de segurança numa fase inicial, quando são relativamente fácil e económica para resolver. Como resultado, reduz significativamente o custo total de desenvolvimento. Além disso, vamos concebidas a ferramenta com especialistas de segurança não em mente, facilitando modelação de ameaça para todos os programadores ao fornecer orientações claras sobre sobre como criar e analisar os modelos de ameaça.

Visite o  **[ferramenta modelação de ameaça](./azure-security-threat-modeling-tool.md)**  para começar a utilizar hoje em dia!

## <a name="mitigation-categories"></a>Categorias de mitigação

As mitigações de ferramenta de modelação de ameaça são categorizadas, de acordo com a Frame de segurança de aplicação Web, que consiste no seguinte:

| Categoria | Descrição |
| -------- | ----------- |
| **[Auditoria e registo](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Quem fez o quê e quando? Registo de auditoria e fazer referência a forma como a aplicação regista eventos relacionados com segurança |
| **[Autenticação](./azure-security-threat-modeling-tool-authentication.md)** | Quem tem a? A autenticação é o processo de onde uma entidade comprova a identidade de outra entidade, normalmente, através de credenciais, tais como o nome de utilizador e palavra-passe |
| **[Autorização](./azure-security-threat-modeling-tool-authorization.md)** | O que fazer? Autorização é como a aplicação fornece controlos de acesso para recursos e operações |
| **[Segurança de comunicação](./azure-security-threat-modeling-tool-communication-security.md)** | Quem é a falar com? Segurança de comunicação assegura que todas as comunicações feita está possíveis |
| **[Gestão de configuração](./azure-security-threat-modeling-tool-configuration-management.md)** | Que executa a aplicação como? As bases de dados que estabelecer ligação ao? Como é administrada a sua aplicação? Como são protegidas estas definições? Gestão de configuração que se refere a forma como a aplicação processa estes problemas operacionais |
| **[Criptografia](./azure-security-threat-modeling-tool-cryptography.md)** | Como é a manter os segredos (confidencialidade)? Como é a verificação do adulterar os dados ou bibliotecas (integridade)? Como são fornecer seeds para valores aleatórios que tem de ser criptograficamente fortes? Criptografia refere-se à forma como a aplicação impõe confidencialidade e integridade |
| **[Gestão de exceções](./azure-security-threat-modeling-tool-exception-management.md)** | Quando uma chamada do método na sua aplicação falha, o que faz a aplicação? Quanto pode revelar? Devolver informações de erro amigável aos utilizadores finais? Transmitir informações da exceção importantes para o autor da chamada? A aplicação falha corretamente? |
| **[Entrada de validação](./azure-security-threat-modeling-tool-input-validation.md)** | Como sabe que a entrada que recebe a sua aplicação é válidos e seguro? Entrada de validação refere-se à forma como a aplicação filtra, scrubs ou rejeita entrada antes do processamento adicional. Considere constraining entrada através de pontos de entrada e saída através de pontos de saída de codificação. Confia dados a partir de origens, como bases de dados e partilhas de ficheiros? |
| **[Dados confidenciais](./azure-security-threat-modeling-tool-sensitive-data.md)** | Como a sua aplicação processar dados confidenciais? Os dados confidenciais refere-se à forma como a aplicação processa todos os dados que devem ser protegidos na memória, através da rede, ou nos arquivos persistentes |
| **[Gestão de sessão](./azure-security-threat-modeling-tool-session-management.md)** | Como a sua aplicação processar e proteger as sessões de utilizador? Uma sessão refere-se a uma série de relacionados interações entre um utilizador e a sua aplicação Web |

Isto ajuda a identificar:

* Onde é efetuadas as mais comuns prende
* Onde estão os melhoramentos mais acionáveis

Como resultado, pode utiliza estas categorias concentrar-se e dar prioridade ao seu trabalho de segurança, para que o se souber que os problemas de segurança atuais mais predominantes ocorrer nas categorias entradas de validação, a autenticação e autorização, pode iniciar não existe. Para obter mais informações, visite  **[esta ligação patente](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Passos seguintes

Visite  **[ameaças de ferramenta de modelação de ameaça](./azure-security-threat-modeling-tool-threats.md)**  para saber mais sobre as categorias de ameaça utiliza a ferramenta para gerar ameaças de design possíveis.