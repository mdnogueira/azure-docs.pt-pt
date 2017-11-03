---
title: "Máquinas virtuais do Centro de segurança do Azure e o Windows no Azure | Microsoft Docs"
description: "Saiba mais sobre a segurança para a máquina virtual do Azure Windows com o Centro de segurança do Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: adb00e28b0b204858a763f83836ee2ac96f8f9e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Monitorizar a segurança da máquina virtual utilizando o Centro de segurança do Azure

Centro de segurança do Azure pode ajudar a obter visibilidade para o recurso do Azure práticas de segurança. Centro de segurança oferece monitorização de segurança integrada. -Pode detetar ameaças que caso contrário podem passar despercebidas. Neste tutorial, pode obter informações sobre o Centro de segurança do Azure e como:
 
> [!div class="checklist"]
> * Configurar a recolha de dados
> * Configurar políticas de segurança
> * Ver e corrigir problemas de estado de funcionamento de configuração
> * Reveja as ameaças detetadas  

## <a name="security-center-overview"></a>Descrição geral do Centro de segurança

Centro de segurança identifica potenciais problemas de configuração de máquina virtual (VM) e direcionados ameaças de segurança. Estes podem incluir as VMs que estão em falta grupos de segurança de rede, os discos não encriptados e ataques de protocolo RDP (Remote Desktop Protocol) de força bruta. As informações são mostradas no dashboard do Centro de segurança em gráficos de fácil leitura.

Para aceder ao dashboard do Centro de segurança, no portal do Azure, no menu, selecione **Centro de segurança**. No dashboard, pode ver o estado de funcionamento de segurança do seu ambiente do Azure, encontrar uma contagem de recomendações atuais e ver o estado atual de alertas de ameaça. Pode expandir cada gráfico de alto nível para ver mais detalhes.

![Dashboard do Centro de segurança](./media/tutorial-azure-security/asc-dash.png)

Centro de segurança vai mais além de deteção de dados para fornecer recomendações para problemas que Deteta. Por exemplo, se uma VM foi implementada sem um grupo de segurança de rede ligado, o Centro de segurança apresenta uma recomendação, com passos de remediação que pode efetuar. Obter a remediação automática sem sair o contexto do Centro de segurança.  

![Recomendações](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Configurar a recolha de dados

Antes de poder obter visibilidade para configurações de segurança VM, terá de configurar a recolha de dados do Centro de segurança. Isto envolve a ativação da recolha de dados e criar uma conta de armazenamento do Azure para armazenar os dados recolhidos. 

1. No dashboard do Centro de segurança, clique em **política de segurança**e, em seguida, selecione a sua subscrição. 
2. Para **recolha de dados**, selecione **no**.
3. Para criar uma conta de armazenamento, selecione **escolher uma conta de armazenamento**. Em seguida, selecione **OK**.
4. No **política de segurança** painel, selecione **guardar**. 

O agente de recolha de dados do Centro de segurança, em seguida, é instalado em todas as VMs e começa a recolha de dados. 

## <a name="set-up-a-security-policy"></a>Configurar uma política de segurança

Políticas de segurança são utilizadas para definir os itens para os quais o Centro de segurança recolhe dados e faz recomendações. Pode aplicar políticas de segurança diferentes para diferentes conjuntos de recursos do Azure. Embora por predefinição os recursos do Azure são avaliados em relação a todos os itens de política, pode desativar os itens de política individuais para todos os recursos do Azure ou para um grupo de recursos. Para obter informações aprofundadas sobre as políticas de segurança do Centro de segurança, consulte [definir políticas de segurança no Centro de segurança do Azure](../../security-center/security-center-policies.md). 

Para configurar uma política de segurança para todos os recursos do Azure:

1. No dashboard do Centro de segurança, selecione **política de segurança**e, em seguida, selecione a sua subscrição.
2. Selecione **política de prevenção**.
3. Ativar ou desativar os itens de política que pretende aplicar a todos os recursos do Azure.
4. Quando tiver terminado de selecionar as definições, selecione **OK**.
5. No **política de segurança** painel, selecione **guardar**. 

Para configurar uma política para um grupo de recursos específico:

1. No dashboard do Centro de segurança, selecione **política de segurança**e, em seguida, selecione um grupo de recursos.
2. Selecione **política de prevenção**.
3. Ativar ou desativar os itens de política que pretende aplicar ao grupo de recursos.
4. Em **HERANÇA**, selecione **Unique**.
5. Quando tiver terminado de selecionar as definições, selecione **OK**.
6. No **política de segurança** painel, selecione **guardar**.  

Também pode desativar a recolha de dados para um grupo de recursos específico nesta página.

No exemplo seguinte, uma política exclusiva foi criada para um grupo de recursos denominado *myResoureGroup*. Nesta política, o disco de encriptação e web aplicação firewall recomendações estão desativadas.

![Exclusivo da política](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Ver estado de funcionamento de configuração de VM

Depois de ter ativada a recolha de dados e definir uma política de segurança, o Centro de segurança começa a disponibilizar recomendações e alertas. Como VMs implementadas, o agente de recolha de dados está instalado. Centro de segurança, em seguida, é preenchido com os dados para as VMs de novo. Para obter informações aprofundadas sobre o estado de funcionamento de configuração de VM, consulte [proteger as suas VMs no Centro de segurança](../../security-center/security-center-virtual-machine-recommendations.md). 

Como os dados são recolhidos, o estado de funcionamento de recursos para cada VM e os recursos do Azure relacionados é agregado. As informações mostradas num gráfico de fácil leitura. 

Para ver o estado de funcionamento de recursos:

1.  A segurança do Centro de dashboard, em **estado de funcionamento de segurança de recursos**, selecione **computação**. 
2.  No **computação** painel, selecione **máquinas virtuais**. Esta vista fornece um resumo do Estado de configuração para todas as suas VMs.

![Estado de funcionamento de computação](./media/tutorial-azure-security/compute-health.png)

Para ver todas as recomendações para uma VM, selecione a VM. Recomendações e remediação são abordadas mais detalhadamente na secção seguinte deste tutorial.

## <a name="remediate-configuration-issues"></a>Resolver problemas de configuração

Depois de centro de segurança começa a preencher com dados de configuração, as recomendações são efetuadas com base na política de segurança que configurou. Por exemplo, se uma VM foi configurado sem um grupo de segurança de rede associadas, é feita uma recomendação para criar um. 

Para ver uma lista de todas as recomendações: 

1. No dashboard do Centro de segurança, selecione **recomendações**.
2. Selecione uma recomendação específica. É apresentada uma lista de todos os recursos para o qual se aplica a recomendação.
3. Para aplicar a recomendação, selecione um recurso específico. 
4. Siga as instruções para obter os passos de remediação. 

Em muitos casos, o Centro de segurança fornece acionáveis passos que pode tomar para resolver uma recomendação sem sair do Centro de segurança. No exemplo seguinte, o Centro de segurança Deteta um grupo de segurança de rede que tenha uma regra de entrada sem restrições. Na página de recomendação, pode selecionar o **editar regras de entrada** botão. A IU do que é necessário modificar a regra é apresentada. 

![Recomendações](./media/tutorial-azure-security/remediation.png)

Como são resolvidas as recomendações, são marcados como resolvido. 

## <a name="view-detected-threats"></a>Ver ameaças detetadas

Para além das recomendações de configuração do recurso, o Centro de segurança apresenta os alertas de deteção de ameaças. A funcionalidade de alertas de segurança agrega os dados recolhidos a partir de cada VM, registos de rede do Azure e soluções de parceiros ligadas para detetar ameaças de segurança relativamente aos recursos do Azure. Para obter informações aprofundadas sobre as capacidades de deteção de ameaças do Centro de segurança, consulte [as capacidades de deteção do Centro de segurança do Azure](../../security-center/security-center-detection-capabilities.md).

A funcionalidade de alertas de segurança requer o Centro de segurança escalão de preço de ser aumentada de *livres* para *padrão*. 30 dias **avaliação gratuita** está disponível quando mover para este escalão de preço superior. 

Para alterar o escalão de preço:  

1. No dashboard do Centro de segurança, clique em **política de segurança**e, em seguida, selecione a sua subscrição.
2. Selecione **escalão de preço**.
3. Selecione o novo escalão e, em seguida, selecione **selecione**.
4. No **política de segurança** painel, selecione **guardar**. 

Depois de alterar o escalão de preço, o gráfico de alertas de segurança começa a preencher que são detetadas ameaças de segurança.

![Alertas de segurança](./media/tutorial-azure-security/security-alerts.png)

Selecione um alerta para ver informações. Por exemplo, pode ver uma descrição de ameaças, a hora da deteção, todas as tentativas de ameaça e a remediação recomendada. No exemplo seguinte, foi detetado um ataque de força bruta RDP, com 294 tentativas falhadas de RDP. É fornecida uma resolução recomendada.

![Ataque RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, pode configurar o Centro de segurança do Azure e, em seguida, revisto VMs no Centro de segurança. Aprendeu a:

> [!div class="checklist"]
> * Configurar a recolha de dados
> * Configurar políticas de segurança
> * Ver e corrigir problemas de estado de funcionamento de configuração
> * Reveja as ameaças detetadas

Avançar para o próximo tutorial para saber como criar um pipeline de CI/CD com uma VM do Windows que executa o IIS e o Visual Studio Team Services.

> [!div class="nextstepaction"]
> [Visual Studio Team Services CI/CD pipeline](./tutorial-vsts-iis-cicd.md)
