---
title: "Atualizar para o escalão Standard do Centro de segurança para a segurança avançada | Microsoft Docs"
description: "Este artigo fornece informações sobre preços do Centro de segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: terrylan
ms.openlocfilehash: 0a8fb526602692db6737842c24649b686bea5dad
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Atualizar para o escalão Standard do Centro de segurança para a segurança avançada
Centro de segurança do Azure fornece gestão unificada de segurança e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e em outros nuvens. Fornece visibilidade e controlo sobre híbridos na nuvem cargas de trabalho, defesas Active Directory que reduzir a exposição a ameaças e deteção inteligente para o ajudar a manter o ritmo com rapidamente evolução de ataques de informático.

## <a name="pricing-tiers"></a>Escalões de preço
Centro de segurança é oferecido na duas camadas:

- O **livres** camada é ativada automaticamente em todas as subscrições do Azure e fornece a política de segurança, avaliação de segurança contínua e recomendações de segurança passíveis de ação para o ajudar a proteger os seus recursos do Azure.
- O **padrão** camada expande as capacidades do escalão gratuito para cargas de trabalho em execução no privadas e outras nuvens públicas, fornecendo unificado proteção de gestão e ameaças de segurança entre as cargas de trabalho de nuvem híbrida. O escalão Standard também adiciona advanced threat as capacidades de deteção, que utiliza a análise comportamental incorporada e machine learning para identificar ataques e exploits de dia zero, controlos de acesso e a aplicação para reduzir a exposição a ataques de rede e de software maligno, e mais. O escalão Standard é gratuito durante os primeira 60 dias.

Para obter mais informações, consulte o Centro de segurança [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="try-standard-free-for-60-days"></a>Tente padrão livre durante 60 dias
O escalão Standard é gratuito durante os 60 dias iniciais. No final de 60 dias, deverá optar por continuar a utilizar o serviço, iremos irá iniciar automaticamente charging para utilização.

Pode atualizar uma subscrição do Azure completa para o escalão padrão, que é herdado por todos os recursos dentro da subscrição, ou pode definir uma política exclusiva para atualizar apenas um grupo de recursos específico.

Para obter o escalão padrão:

1. Selecione **política de segurança** no **Centro de segurança** menu principal.
2. Selecione a subscrição que pretende atualizar para o padrão.
3. No **política de segurança** painel, selecione **escalão de preço**.
4. Selecione **padrão** para atualizar.
5. Clique em **Guardar**.

![Incidente de segurança][1]

> [!NOTE]
> Para ativar todas as funcionalidades do Centro de segurança, tem de aplicar a camada para a subscrição ou grupo de recursos que contém as máquinas virtuais aplicáveis de preço padrão. Não ative a configuração de preços para uma área de trabalho a apenas no acesso VM de tempo, os controlos de aplicação adaptável e deteções de rede para recursos do Azure.
>
>

## <a name="why-upgrade-to-standard"></a>Por que razão a atualização para Standard?
Centro de segurança oferece maior segurança e proteção contra ameaças do seu híbridos na nuvem cargas de trabalho, incluindo:

- **Segurança híbrida** – obter uma vista unificada de segurança em todos os seus no local e na nuvem cargas de trabalho. Aplicar políticas de segurança e avaliar continuamente a segurança das suas cargas de trabalho de nuvem híbrida para garantir a conformidade com as normas de segurança. Recolha, pesquise e analise os dados de segurança de várias origens, incluindo firewalls e outras soluções de parceiros.
- **A deteção de ameaças de avançadas** -utilização avançada de análise e o gráfico de segurança inteligente da Microsoft para obter um limite através de evolução de ataques de informático.  Tire partido da análise comportamental e do machine learning incorporados para identificar ataques e explorações de dia zero. Monitorize redes, máquinas e serviços cloud para ataques recebidos e atividade pós-falhas. Uniformize a investigação com ferramentas interativas e informações sobre ameaças contextuais.
- **Controlos de acesso e a aplicação** -software maligno de bloco e outras aplicações indesejáveis aplicando a listas brancas recomendações adaptada descritos para as cargas de trabalho específicas e utiliza a tecnologia de aprendizagem. Reduza a superfície de ataque rede com acesso just-in-time, controlada para as portas de gestão em VMs do Azure, reduzindo significativamente a exposição a força bruta e outros ataques de rede.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, foram introduzidas preços para o Centro de segurança. Para saber mais sobre a segurança avançada e proteção avançada contra ameaças do escalão Standard, consulte:

- [Deteção avançada de ameaças](security-center-threat-report.md)
- [Apenas no controlo de acesso VM de tempo](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
