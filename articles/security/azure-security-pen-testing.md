---
title: Teste de caneta | Microsoft Docs
description: "O artigo fornece uma descrição geral de penetração testar o processo de (pentest) e como executar pentest contra as suas aplicações em execução na infraestrutura do Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: yurid
ms.openlocfilehash: 070e848f753452953b9e5dfe94799e7c0a314530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="pen-testing"></a>Teste de caneta
É uma das ações excelente sobre como utilizar o Microsoft Azure para implementação e teste de aplicações que não é necessário juntar-se de uma infraestrutura no local para desenvolver, testar e implementar as suas aplicações. Toda a infraestrutura está tratada, os serviços da plataforma Microsoft Azure. Não tem de se preocupar com requisitioning, aquisição, "racking e empilhamento" hardware os seus próprios no local.

Esta é uma grande – mas ainda tem de certificar-se de que efetua a segurança normal diligence devida. Uma das coisas que precisa de fazer é penetração teste as aplicações que implementa no Azure.

Já poderá saber que a Microsoft efetua [penetração testes do nosso ambiente do Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Isto ajuda-na melhorar a nossa plataforma e orienta as nossas ações em termos de melhorar os controlos de segurança, a introduzir novos controlos de segurança e melhorar o nosso processos de segurança.

Iremos não o caneta testar a aplicação por si, mas compreendemos que irá pretender e terá de executar as suas próprias aplicações de teste de caneta. É uma boa coisa, porque quando melhorar a segurança das suas aplicações, de ajudar a tornar a todo o ecossistema do Azure mais seguros.

Quando a caneta testar as suas aplicações, este aspeto que poderá ter um ataque para nós. Iremos [monitorizar continuamente](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) para padrões de ataques e iniciar um processo de resposta a incidentes se é necessário. -Não ajudá-lo e não ajudar-nos se acionamos uma resposta a incidentes devido a sua própria devido caneta diligence testar.

O que fazer?

Quando estiver pronto para caneta testar as aplicações alojado no Azure, tem uma opção para [informe-nos](https://portal.msrc.microsoft.com/en-us/engage/pentest). Depois de Sabemos que vai efetuar testes específicos, iremos não inadvertidamente encerrar, (por exemplo, a bloquear o endereço IP que está a testar do), desde que os seus testes em conformidade com a Azure caneta testar termos e condições descritas [ Microsoft Cloud Unified penetração testar regras do Engagement](https://technet.microsoft.com/en-us/mt784683).
Testes padrão, que pode realizar incluem:

* Os testes no seus pontos finais para descobrir o [10 vulnerabilidades de principais de aplicação do abra Web projeto segurança (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzz testar](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) dos seus pontos finais
* [Análise de porta](https://en.wikipedia.org/wiki/Port_scanner) dos seus pontos finais

É de um tipo de teste que não é possível executar quaisquer tipos de [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) ataque. Isto inclui a iniciar um ataque DoS próprio ou efetuar testes relacionados que podem determinar, demonstrar ou simular qualquer tipo de um ataque.

Pronto para começar a caneta irá testar as suas aplicações alojadas no Microsoft Azure? Se Sim, em seguida, vá na ativação pós-falha para o [descrição geral do teste penetração](https://technet.microsoft.com/library/mt784683.aspx) página (e clique em criar uma pedido de teste na parte inferior da página. Também irá encontrar mais informações sobre a caneta testar termos e condições e hiperligações úteis como pode reportar falhas de segurança relacionados com o Azure ou qualquer outro serviço da Microsoft.
