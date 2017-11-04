---
title: "Integração do Azure Security Center Standard para a segurança avançada | Microsoft Docs"
description: " Saiba como a carregar para o Azure Security Center Standard para avançada segurança. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: a6394b1b02ebfe518dc2f2b7f65eb677bb0ba5f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Integração do Azure Security Center Standard para a segurança avançada
Atualizar para o Security Center Standard para tirar partido da gestão de segurança avançada e proteção contra ameaças para as cargas de trabalho de nuvem híbrida.  Pode tentar padrão livre durante 60 dias. Consulte o Centro de segurança [página de preços](https://azure.microsoft.com/pricing/details/security-center/) para obter mais informações.

Padrão de centro de segurança inclui:

- **Segurança híbrida** – obter uma vista unificada de segurança em todos os seus no local e na nuvem cargas de trabalho. Aplicar políticas de segurança e avaliar continuamente a segurança das suas cargas de trabalho de nuvem híbrida para garantir a conformidade com as normas de segurança. Recolha, pesquise e analise os dados de segurança de várias origens, incluindo firewalls e outras soluções de parceiros.
- **A deteção de ameaças de avançadas** -utilização avançada de análise e o gráfico de segurança inteligente da Microsoft para obter um limite através de evolução de ataques de informático.  Tire partido da análise comportamental e do machine learning incorporados para identificar ataques e explorações de dia zero. Monitorize redes, máquinas e serviços cloud para ataques recebidos e atividade pós-falhas. Uniformize a investigação com ferramentas interativas e informações sobre ameaças contextuais.
- **Controlos de acesso e a aplicação** -software maligno de bloco e outras aplicações indesejáveis aplicando a listas brancas recomendações adaptada descritos para as cargas de trabalho específicas e utiliza a tecnologia de aprendizagem. Reduza a superfície de ataque rede com acesso just-in-time, controlada para as portas de gestão em VMs do Azure, reduzindo significativamente a exposição a força bruta e outros ataques de rede.

## <a name="detecting-unprotected-resources"></a>Detetar recursos desprotegidos     
Centro de segurança Deteta automaticamente quaisquer subscrições do Azure ou não ativadas para padrão de centro de segurança de áreas de trabalho. Isto inclui as subscrições do Azure utilizando o Centro de segurança livre e áreas de trabalho que não têm a solução de segurança ativada.

Pode atualizar uma subscrição do Azure completa para o escalão padrão, que é herdado por todos os recursos dentro da subscrição, ou pode definir uma política exclusiva para atualizar apenas um grupo de recursos específico. Se as definições de política de grupo de recursos são exclusivas, o Centro de segurança não irá substituir as políticas de preços ao atualizar a subscrição para o escalão Standard. Aplicar o padrão de camada para uma subscrição aplica-se apenas às VMs na subscrição que estiverem a relatar a áreas de trabalho criadas pelo centro de segurança. Aplicar o padrão de camada para a área de trabalho que se aplica a todos os recursos para a área de trabalho de relatórios.

> [!NOTE]
> Poderá querer gerir os custos e limitar a quantidade de dados recolhidos para uma solução, limitando-la a um conjunto específico de agentes. [Filtragem de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite-lhe aplicar um âmbito para a solução e um subconjunto de computadores na área de trabalho de destino.  Se estiver a utilizar a solução de filtragem, o Centro de segurança lista da área de trabalho como não tendo uma solução.
>
>

## <a name="upgrade-an-azure-subscription"></a>Atualizar uma subscrição do Azure
Para atualizar todas as subscrições para padrão:
1. No menu principal do Centro de segurança, selecione **integração**.
2. Em **integração de segurança avançada**, o Centro de segurança apresenta uma lista de subscrições elegíveis para a integração. Pode atualizar todas as subscrições listadas selecionando **plano aplicar padrão**.

  ![Atualizar todas as subscrições][1]

Para atualizar uma subscrição individuais para Standard: pode atualizar uma subscrição do **integração** selecionando **aplicar escalão Standard**. Para atualizar um grupo de recursos sob a subscrição para padrão, selecione a subscrição:
1. Selecione uma subscrição.  **Política de segurança** fornece informações sobre o grupo de recursos contido na subscrição.
2. Selecione a subscrição ou um grupo de recursos.

  ![Atualizar todas as subscrições][2]

3. Selecione **padrão** a atualização do gratuito para Standard.
4. Selecione **Guardar**.

> [!NOTE]
> Atualizar uma subscrição para Standard irá ativar o [aprovisionamento automático](security-center-enable-data-collection.md) se tiver sido desativada anteriormente. Recomendamos que o aprovisionamento automático de agentes de monitorização.
>
>

## <a name="upgrade-a-workspace"></a>Atualizar uma área de trabalho
Aplicar padrão para a área de trabalho aplicam-se a todos os recursos para a área de trabalho de relatórios.

1. Volte à **integração** painel.
2. Selecione uma área de trabalho.

  ![Atualizar uma área de trabalho][8]

3. Selecione **padrão** para atualizar.  
4. Selecione **Guardar**.

   > [!NOTE]
   > Não há um cenário onde poderá não ter gratuito ou Standard aplicadas a sua área de trabalho. Se selecionar gratuito, capacidades livre do Centro de segurança são aplicadas apenas as VMs do Azure. As capacidades livres não são aplicadas aos computadores não do Azure. Se selecionar padrão, as capacidades de padrão são aplicadas a todas as VMs do Azure e computadores não do Azure para a área de trabalho de relatórios. Recomendamos que aplique padrão para fornecer segurança avançada para o Azure e os recursos do Azure não.
   >
   >

## <a name="onboard-non-azure-computers"></a>Integrar computadores de não do Azure
O Centro de Segurança pode monitorizar a postura de segurança dos seus computadores não Azure, mas, primeiro, tem de incluir esses recursos. Pode adicionar computadores não do Azure a partir do **integração** painel ou a partir de **computação** painel. Vamos explicar através de ambos os métodos.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Adicionar novos computadores não do Azure a partir de integração

1. Volte ao **integração**.   
2. Selecione **que pretende adicionar novos computadores não Azure**.

  ![Adicionar o computador não do Azure][3]

Se tiver de áreas de trabalho existentes, estão listados na **adicionar novos computadores não-Azure**. Pode adicionar computadores a uma área de trabalho existente ou criar uma nova área de trabalho. Para criar uma nova área de trabalho, selecione a ligação **adicionar uma nova área de trabalho**.

Vamos explicar através de ambos os métodos:

- Crie uma nova área de trabalho e adicione o computador
- Selecione uma área de trabalho existente e adicionar o computador

**Crie uma nova área de trabalho e adicione o computador**

1. Em **adicionar novos computadores não Azure**, selecione **adicionar uma nova área de trabalho**.

   ![Adicionar uma nova área de trabalho][4]

2. Em **auditoria e segurança**, selecione **área de trabalho OMS** para criar uma nova área de trabalho.
3. Em **área de trabalho OMS**, introduza as informações para a sua área de trabalho.
4. Em **área de trabalho OMS**, selecione **OK**.  Depois de selecionar OK, obterá uma hiperligação para transferir um agente do Windows ou Linux e chaves para o seu ID da área de trabalho utilizar na configuração do agente.
5. Em **auditoria e segurança**, selecione **OK**.

**Selecione uma área de trabalho existente e adicionar o computador**

Pode adicionar um computador ao seguir o fluxo de trabalho de **integração**, conforme mostrado acima. Também pode adicionar um computador ao seguir o fluxo de trabalho de **computação**. Neste exemplo, utilizamos **computação**.

1. Regressar ao menu principal do Centro de segurança e **descrição geral** dashboard.

   ![Descrição geral][5]

2. Selecione o **computação** mosaico.
3. Em **computação**, selecione **adicionar computadores**.

   ![Painel de computação][6]

4. Em **adicionar novos computadores não Azure**, selecione uma área de trabalho para ligar o computador e clique em **adicionar computadores**.

   ![Adicionar computadores][7]

 O **agente direta** painel fornece uma hiperligação para transferir um agente do Windows ou Linux e chaves para o seu ID da área de trabalho utilizar na configuração do agente.   

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu como integrar do Azure e de recursos não do Azure para beneficiar de segurança avançada do Centro de segurança.  Para fazer mais com os recursos de integradas, consulte

- [Ativar a recolha de dados](security-center-enable-data-collection.md)
- [Relatório de informações sobre ameaças](security-center-threat-report.md)
- [Apenas no acesso VM de tempo](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
