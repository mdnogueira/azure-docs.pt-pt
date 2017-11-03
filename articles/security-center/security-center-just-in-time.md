---
title: "Apenas na máquina virtual de tempo de acesso no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento explica como através de tal como no tempo VM aceder no Centro de segurança do Azure ajuda a controla o acesso às máquinas virtuais do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: terrylan
ms.openlocfilehash: c715afe55a3aedd5c4f826bc34c3c56e167d2f82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-virtual-machine-access-using-just-in-time-preview"></a>Gerir o acesso de máquina virtual utilizando apenas no tempo (pré-visualização)

Apenas na máquina virtual de tempo (VM), acesso pode ser utilizado para bloquear o tráfego de entrada para as VMs do Azure, reduzir a exposição a ataques, fornecendo acesso fácil para ligar a VMs, quando necessário.

> [!NOTE]
> O apenas na hora funcionalidade em pré-visualização e está disponível na camada padrão do Centro de segurança.  Consulte [preços](security-center-pricing.md) para saber mais acerca do Centro de segurança do escalões de preço.
>
>

## <a name="attack-scenario"></a>Cenário de ataque

Força bruta de ataques geralmente as portas de gestão de destino como um meio para obter acesso a uma VM. Se tiver êxito, um atacante pode assumir o controlo sobre a VM e estabelecer a sua posição no ambiente.

Uma forma para reduzir a exposição a um ataque de força bruta é para limitar a quantidade de tempo que uma porta está aberta. As portas de gestão não precisam de estar aberta permanente. Apenas têm de estar aberta enquanto estiverem ligados à VM, por exemplo, para efetuar tarefas de gestão ou manutenção. Quando apenas na hora estiver ativada, o Centro de segurança utiliza [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) regras (NSG), que restringem o acesso às portas de gestão, pelo que não pode ser visados pelos atacantes.

![Apenas no cenário de tempo][1]

## <a name="how-does-just-in-time-access-work"></a>Como tal acesso time trabalhar?

Quando o just in time estiver ativado, o Centro de Segurança bloqueia o tráfego de entrada para as suas VMs do Azure através da criação de uma regra NSG. Selecione as portas na VM para o qual vai ser bloqueado a tráfego de entrada. Estas portas são controladas mediante a apenas na solução de tempo.

Quando um utilizador solicita acesso a uma VM, o Centro de segurança verifica se o utilizador tem [controlo de acesso baseado em funções (RBAC)](../active-directory/role-based-access-control-configure.md) permissões que fornecem acesso de escrita para a VM. Se têm permissões de escrita, o pedido é aprovado e o Centro de segurança configura automaticamente os grupos de segurança de rede (NSGs) para permitir tráfego de entrada para as portas de gestão para a quantidade de tempo especificado. Depois do tempo expirou, o Centro de segurança restaura os NSGs para os respetivos Estados de anteriores.

> [!NOTE]
> Centro de segurança apenas do acesso VM de tempo atualmente suporta apenas as VMs implementadas através do Azure Resource Manager. Para obter mais informações sobre clássica e modelos de implementação do Resource Manager, consulte [vs. de implementação clássica do Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Utilizar apenas o acesso de tempo

O **apenas acesso de VM de tempo** mosaico em **Centro de segurança** mostra o número de VMs configuradas para apenas no acesso do tempo e o número de aprovação de pedidos de acesso efetuados na última semana.

![Apenas no tempo de acesso VM mosaico][2]

Selecione o **apenas acesso de VM de tempo** mosaico e **apenas acesso de VM de tempo** abre.

![Apenas no tempo de acesso VM mosaico][10]

**Apenas no acesso VM de tempo** fornece informações sobre o estado das suas VMs:

- **Configurado** -VMs que tenham sido configuradas para suportar apenas de acesso VM de tempo. Os dados apresentados é da última semana e incluem para cada VM, o número de pedidos aprovados, a data do último acesso e a hora e como último utilizador.
- **Recomendado** -VMs que podem suportar apenas no acesso VM de tempo, mas não foram configuradas para. Recomendamos que ative apenas no controlo de acesso VM de tempo para estas VMs. Consulte [configurar uma política de acesso de tempo](#configuring-a-just-in-time-access-policy).
- **Nenhuma recomendação** -razões que podem causar uma VM para não ser recomendado são:
  - Falta o NSG - o apenas no tempo a solução requer um NSG a ser implementada.
  - VMS clássicas - o Centro de segurança apenas do acesso VM de tempo atualmente suporta apenas as VMs implementadas através do Azure Resource Manager. Uma implementação clássica não é suportada pelo apenas na solução de tempo.
  - Outros - uma VM está nesta categoria se a apenas no tempo solução está desativada na política de segurança da subscrição ou o grupo de recursos ou se a VM está em falta um IP público e não tem um NSG no local.

## <a name="configuring-a-just-in-time-access-policy"></a>Configurar uma política de acesso de tempo

Para selecionar as VMs que pretende ativar:

1. Em **apenas acesso de VM de tempo**, selecione o **recomendado** separador.

  ![Ativar apenas no acesso do tempo][3]

2. Em **máquina VIRTUAL**, selecione as VMs que pretende ativar. Isto coloca uma marca de verificação junto a uma VM.
3. Selecione **ativar JIT em VMs**.
4. Selecione **Guardar**.

### <a name="default-ports"></a>Portas predefinidas

Pode ver as portas predefinidas, que o Centro de segurança recomenda a ativação apenas no tempo.

1. Em **apenas acesso de VM de tempo**, selecione o **recomendado** separador.

  ![Apresentar as portas predefinidas][6]

2. Em **VMs**, selecione uma VM. Isto coloca uma marca de verificação junto a VM e abre **configuração de acesso de VM de JIT**. Este painel mostra as portas predefinidas.

### <a name="add-ports"></a>Adicionar portas

Em **configuração de acesso de VM de JIT**, também pode adicionar e configurar uma porta de novo no qual pretende ativar a apenas na solução de tempo.

1. Em **configuração de acesso de VM de JIT**, selecione **adicionar**. Esta ação abre **configuração da porta adicionar**.

  ![Configuração da porta][7]

2. Em **configuração da porta adicionar**, que identifica a porta, tipo de protocolo, origem permitida IPs e tempo máximo de pedido.

  Origem permitida IPs são os intervalos IP permitidos aceder após um pedido aprovado.

  Tempo máximo de pedido é o intervalo de tempo máximo que pode ser aberta uma porta específica.

3. Selecione **OK**.

## <a name="requesting-access-to-a-vm"></a>Pedir acesso a uma VM

Para pedir acesso a uma VM:

1. Em **apenas acesso de VM de tempo**, selecione o **configurado** separador.
2. Em **VMs**, selecione as VMs que pretende ativar o acesso. Isto coloca uma marca de verificação junto a uma VM.
3. Selecione **pedir acesso**. Esta ação abre **pedir acesso**.

  ![Pedir acesso a uma VM][4]

4. Em **pedir acesso**, configurar para cada VM as portas para abrir juntamente com o IP de origem que a porta está aberta e a janela de tempo para que a porta está aberta. Pode pedir acesso apenas para as portas que são configuradas no apenas na política de tempo. Cada porta tem um máximo de tempo que deriva de apenas permitido na política de tempo.
5. Selecione **abrir portas**.

## <a name="editing-a-just-in-time-access-policy"></a>Editar uma política de acesso de tempo

Pode alterar uma VM existente apenas na política de tempo ao adicionar e configurar uma nova porta abrir para essa VM ou ao alterar quaisquer outros parâmetros relacionados com uma porta já protegida.

Para editar uma existente apenas na política de tempo de uma VM, o **configurado** separador é utilizado:

1. Em **VMs**, selecione uma VM para adicionar uma porta para clicando nos três pontos na linha para essa VM. Esta ação abre um menu.
2. Selecione **editar** no menu. Esta ação abre **configuração de acesso de VM de JIT**.

  ![Editar política][8]

3. Em **configuração de acesso de VM de JIT**, pode editar as definições existentes de uma porta já protegida ou clicando na respetiva porta ou pode selecionar **adicionar**. Esta ação abre **configuração da porta adicionar**.

  ![Adicionar uma porta][7]

4. Em **configuração da porta adicionar**, identificar a porta, tipo, tempo de pedido de IPs de origem e o máximo permitido de protocolo.
5. Selecione **OK**.
6. Selecione **Guardar**.

## <a name="auditing-just-in-time-access-activity"></a>Auditoria apenas na atividade de acesso de hora

Pode obter informações sobre as atividades VM com pesquisa de registo. Para ver registos:

1. Em **apenas acesso de VM de tempo**, selecione o **configurado** separador.
2. Em **VMs**, selecione uma VM para ver informações sobre clicando nos três pontos na linha para essa VM. Esta ação abre um menu.
3. Selecione **registo de atividade** no menu. Esta ação abre **registo de atividade**.

  ![Selecione o registo de atividade][9]

  **Registo de atividade** fornece uma vista filtrada de operações anteriores para essa VM juntamente com a hora, a data e a subscrição.

  ![Ver o registo de atividade][5]

Pode transferir as informações de registo selecionando **clique aqui para transferir todos os itens como CSV**.

Modificar os filtros e selecione **aplicar** para criar uma pesquisa e o registo.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Utilizar apenas no tempo de acesso VM através do PowerShell

Para poder utilizar o apenas na solução de tempo através do PowerShell, certifique-se de que tem o [mais recente](/powershell/azure/install-azurerm-ps) Azure PowerShell versão.
Quando o fizer, terá de instalar o [mais recente](https://aka.ms/asc-psgallery) Centro de segurança do Azure da galeria do PowerShell.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Configurar uma política de tempo para uma VM

Para configurar uma política de tempo numa VM específica, terá de executar este comando na sua sessão do PowerShell: ASCJITAccessPolicy de conjunto.
Siga a documentação do cmdlet para saber mais.

### <a name="requesting-access-to-a-vm"></a>Pedir acesso a uma VM

Para aceder a uma VM específica que está protegida com a solução de hora, terá de executar este comando na sua sessão do PowerShell: ASCJITAccess invocar.
Siga a documentação do cmdlet para saber mais.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu como apenas no tempo de acesso VM no Centro de segurança ajuda a controla o acesso às máquinas virtuais do Azure.

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- [Definir políticas de segurança](security-center-policies.md) — Saiba como configurar políticas de segurança para as subscrições do Azure e os grupos de recursos.
- [Gerir recomendações de segurança](security-center-recommendations.md) — Saiba como recomendações ajudam a proteger os seus recursos do Azure.
- [Monitorização de estado de funcionamento de segurança](security-center-monitoring.md) — Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e responder a alertas de segurança](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
- [Monitorizar soluções de parceiros](security-center-partner-solutions.md) — Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [FAQ do Centro de segurança](security-center-faq.md) – encontre as perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
