---
title: "Ameaças - ferramenta de modelação de ameaça Microsoft - Azure | Microsoft Docs"
description: "Página de categoria de ameaças para a ferramenta de modelação de ameaça da Microsoft, que contém categorias para todos os expostos gerado ameaças."
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
ms.openlocfilehash: 704f9995828866d4d2e4969e3aa922ed1e23c4ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Ameaças da ferramenta de modelação de ameaça na Microsoft

A ferramenta de modelação de ameaça é um elemento principal do Microsoft Security Development Lifecycle (SDL). Permite arquitetos de software identificar e mitigar os potenciais problemas de segurança numa fase inicial, quando são relativamente fácil e económica para resolver. Como resultado, reduz significativamente o custo total de desenvolvimento. Além disso, vamos concebidas a ferramenta com especialistas de segurança não em mente, facilitando modelação de ameaça para todos os programadores ao fornecer orientações claras sobre sobre como criar e analisar os modelos de ameaça.

> Visite o  **[ferramenta modelação de ameaça](./azure-security-threat-modeling-tool.md)**  para começar a utilizar hoje em dia!

A ferramenta de modelação de ameaça ajuda a responder a algumas perguntas, tais como aqueles abaixo:

* Como é que um atacante pode alterar os dados de autenticação?
* O que é o impacto se um atacante possa ler os dados de perfil de utilizador?
* O que acontece se o acesso é negado para a base de dados de perfil de utilizador?

## <a name="stride-model"></a>STRIDE modelo

A melhor ajuda formular a estes tipos de questões apontados, a Microsoft utiliza o modelo STRIDE, categoriza diferentes tipos de ameaças e simplifica as conversações de segurança geral.

| Categoria | Descrição |
| -------- | ----------- |
| **Spoofing** | Envolve aceder ilegalmente e, em seguida, utilizar informações de autenticação de outro utilizador, tais como o nome de utilizador e palavra-passe |
| **Adulteração** | Envolve a modificação maliciosa de dados. Os exemplos incluem alterações não autorizadas efetuadas aos dados persistentes, tal como contido numa base de dados e a alteração dos dados que fluem entre dois computadores ao longo de uma rede aberta, tal como a Internet |
| **Rejeição** | Associado a utilizadores sem outras entidades ter qualquer forma de comprovar caso contrário a efetuar uma ação de negação — por exemplo, um utilizador efetua uma operação ilegal num sistema que não possui a capacidade para rastrear as operações proibidas. Não rejeição refere-se para a capacidade de um sistema a ameaças de rejeição de contador. Por exemplo, pode ter um utilizador que adquire um item iniciar sessão para o item após a receção. O fornecedor, em seguida, pode utilizar a receção assinada como provas de que o utilizador recebeu o pacote |
| **Divulgação de informações** | Envolve a exposição das informações para utilizadores que não deverá para ter acesso à mesma — por exemplo, a capacidade dos utilizadores para ler um ficheiro que não lhes foi concedido acesso aos, ou a capacidade de um intruso ler os dados em trânsito entre dois computadores |
| **Recusa de serviço** | Serviço de negação de ataques denial of service (DoS) para os utilizadores válidos — por exemplo, ao tornar um servidor Web temporariamente indisponível ou não utilizável. Terá de proteger contra determinados tipos de ameaças DoS simplesmente para melhorar a fiabilidade e disponibilidade de sistema |
| **Elevação de privilégios** | Um utilizador sem privilégios obtém acesso privilegiado e, deste modo, tem acesso suficiente para comprometer ou destruir todo o sistema. Elevação de ameaças de privilégio incluir esses situações em que um atacante efetivamente tem penetrated todos os defesas de sistema e tornam-se parte fidedigno do sistema de si próprio, uma situação perigoso realmente |

## <a name="next-steps"></a>Passos seguintes

Avance para  **[mitigações de ferramenta de modelação de ameaça](./azure-security-threat-modeling-tool-mitigations.md)**  para aprender as formas diferentes pode mitigar estas ameaças com o Azure.