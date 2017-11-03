---
title: Utilize etiquetas formatada em JSON para agendar o estado de VM do Azure | Microsoft Docs
description: Este artigo demonstra como utilizar cadeias JSON no etiquetas para automatizar o agendamento do VM arranque e encerramento.
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
ms.openlocfilehash: cae4020741003be54b133efa121b3c09b859a176
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Cenário de automatização do Azure: utilizar etiquetas formatada em JSON para criar um agendamento para a VM do Azure de arranque e encerramento
Muitas vezes, os clientes pretendem de agendar o arranque e encerramento de máquinas virtuais para ajudar a reduzir os custos de subscrição ou empresariais e requisitos técnicos de suporte.

O seguinte cenário permite-lhe configurar automatizada de arranque e encerramento das suas VMs através da utilização de uma tag denominada agenda a um nível de máquina virtual no Azure ou ao nível do grupo de recursos. Este agendamento pode ser configurado de Domingo a Sábado com um tempo de arranque e o tempo de encerramento.

Temos algumas opções de out of box. Estas incluem:

* [Conjuntos de dimensionamento de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) com definições de dimensionamento automático que permitem-lhe aumentar ou reduzir.
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) serviço, que tem a capacidade incorporada do agendamento de arranque e encerramento operações.

No entanto, estas opções só suportam cenários específicos e não pode ser aplicada às VMs de infraestrutura-como-um-serviço (IaaS).

Quando a tag de agendamento é aplicada a um grupo de recursos, também é aplicada a todas as máquinas virtuais dentro desse grupo de recursos. Se uma agenda também diretamente é aplicada a uma VM, a agenda de último prevalece pela seguinte ordem:

1. Agenda aplicada a um grupo de recursos
2. Agenda aplicada a um grupo de recursos e a máquina virtual no grupo de recursos
3. Agenda aplicada a uma máquina virtual

Neste cenário, essencialmente, demora uma cadeia JSON com formato especificado e adiciona-o como o valor para uma tag denominada agenda. Em seguida, um runbook apresenta uma lista de todos os grupos de recursos e máquinas virtuais e identifica as agendas para cada VM com base nos cenários de listados anteriormente. Em seguida repetido ao longo as VMs que têm agendas ligadas e avalia que deverão ser executadas. Por exemplo, determina que VMs tem de parar, encerrar ou ignoradas.

Estes runbooks autenticar utilizando o [conta Run As do Azure](automation-sec-configure-azure-runas-account.md).

## <a name="download-the-runbooks-for-the-scenario"></a>Transferir runbooks para o cenário
Este cenário consiste em quatro runbooks do fluxo de trabalho do PowerShell que pode transferir a partir de [TechNet Gallery](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) ou [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) repositório para este projeto.

| Runbook | Descrição |
| --- | --- |
| Teste ResourceSchedule |Verifica cada agenda de máquina virtual e efetua o encerramento ou de arranque, consoante a agenda. |
| ResourceSchedule adicionar |Adiciona a tag de agendamento para um VM ou grupo de recursos. |
| Atualização ResourceSchedule |Modifica a tag de agenda existente, substituindo-lo com um novo. |
| Remover ResourceSchedule |Remove a tag de agendamento de um VM ou grupo de recursos. |

## <a name="install-and-configure-this-scenario"></a>Instalar e configurar este cenário
### <a name="install-and-publish-the-runbooks"></a>Instalar e publicar os runbooks
Depois de transferir os runbooks, pode importá-los utilizando o procedimento [criar ou importar um runbook na automatização do Azure](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).  Publica cada runbook após tem foi importada com êxito na sua conta de automatização.

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>Adicionar uma agenda ao runbook ResourceSchedule de teste
Siga estes passos para ativar o agendamento do runbook de teste ResourceSchedule. Este é o runbook que verifica que máquinas virtuais deve ser iniciadas, encerrar ou à esquerda é.

1. No portal do Azure, abra a sua conta de automatização e, em seguida, clique o **Runbooks** mosaico.
2. No **teste ResourceSchedule** painel, clique em de **agendas** mosaico.
3. No **agendas** painel, clique em **adicionar uma agenda**.
4. No **agendas** painel, selecione **associar um agendamento ao runbook**. Em seguida, selecione **criar uma nova agenda**.
5. No **nova agenda** painel, escreva o nome desta agenda, por exemplo: *HourlyExecution*.
6. Para a agenda **iniciar**, defina o período de início como um incremento de hora.
7. Selecione **periodicidade**e, em seguida, para **repetir a cada intervalo**, selecione **1 hora**.
8. Certifique-se de que **definir expiração** está definido como **não**e, em seguida, clique em **criar** para guardar a agenda de novo.
9. No **agenda Runbook** painel de opções, selecione **parâmetros e definições de execução**. No teste-ResourceSchedule **parâmetros** painel, introduza o nome da sua subscrição no **SubscriptionName** campo.  Este é o parâmetro apenas, necessária para o runbook.  Quando tiver terminado, clique em **OK**.

O agendamento do runbook deverá ter um aspeto semelhante ao seguinte quando estiver concluída:

![Runbook de teste ResourceSchedule configurado](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>Formatar a cadeia JSON
Esta solução basicamente demora um JSON da cadeia de formato especificado e adiciona-o como o valor de uma tag denominada agenda. Em seguida, um runbook apresenta uma lista de todos os grupos de recursos e máquinas virtuais e identifica as agendas para cada máquina virtual.

O runbook repete através de máquinas virtuais que têm agendas ligadas e verifica que ações deverão ser executadas. Segue-se um exemplo de como as soluções devem ter o formato:

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

Eis algumas informações detalhadas sobre esta estrutura:

1. O formato da estrutura JSON está otimizado para contornar a limitação de 256 carateres de um valor de etiqueta única no Azure.
2. *TzId* representa o fuso horário da máquina virtual. Este ID pode ser obtido utilizando a classe de TimeZoneInfo .NET uma sessão de PowerShell –**[System.TimeZoneInfo]:: GetSystemTimeZones()**.

   ![GetSystemTimeZones no PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * Dias da semana são representados com um valor numérico de zero para seis. O valor zero é igual a Domingo.
   * A hora de início é representada com o **S** atributo e o respetivo valor é um formato de 24 horas.
   * A hora de fim ou de encerramento é representada com o **i** atributo e o respetivo valor é um formato de 24 horas.

     Se o **S** e **i** atributos cada tem um valor de zero (0), a máquina virtual vai ser deixada no estado presente no momento da avaliação.
3. Se pretender ignorar a avaliação de um dia da semana específico, não adicione uma secção para esse dia da semana. No exemplo seguinte, é avaliada apenas segunda-feira e os outros dias da semana são ignorados:

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>Grupos de recursos de etiqueta ou VMs
Para encerrar VMs, terá de assinalar as VMs ou os grupos de recursos no qual está localizados. Máquinas virtuais que não tenham uma etiqueta de agenda não são avaliadas. Por conseguinte, não são iniciadas ou encerrados.

Existem duas formas de grupos de recursos de etiqueta ou VMs com esta solução. Pode fazê-lo diretamente a partir do portal. Ou pode utilizar a adicionar ResourceSchedule, ResourceSchedule de atualização e remova ResourceSchedule runbooks.

### <a name="tag-through-the-portal"></a>Etiqueta através do portal
Siga estes passos para marcar uma máquina virtual ou grupo de recursos no portal:

1. Aplanar a cadeia JSON e certifique-se de que não sabemos de quaisquer espaços.  A cadeia JSON deve ter o seguinte aspeto:

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. Selecione o **Tag** ícone para um VM ou grupo de recursos aplicar esta agenda.

   ![Opção de etiqueta VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. As etiquetas são definidas seguir um par chave/valor. Tipo **agenda** no **chave** campo e, em seguida, cole a cadeia JSON para o **valor** campo. Clique em **Guardar**. A nova etiqueta deverá agora aparecer na lista de etiquetas para o seu recurso.

   ![Etiqueta de agenda VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>Etiqueta do PowerShell
Todos os runbooks importados contêm informações de ajuda no início do script que descreve como executar os runbooks diretamente a partir do PowerShell. Pode ligar os runbooks adicionar ScheduleResource e ScheduleResource de atualização a partir do PowerShell. Fazê-lo através da transmissão de parâmetros necessários, que lhe permite criar ou atualizar a etiqueta de agenda uma VM ou grupo de recursos fora do portal.

Para criar, adicionar e eliminar etiquetas através do PowerShell, tem primeiro de [configurar o ambiente de PowerShell para o Azure](/powershell/azure/overview). Depois de concluir a configuração, pode continuar com os seguintes passos.

### <a name="create-a-schedule-tag-with-powershell"></a>Criar uma etiqueta de agenda com o PowerShell
1. Abra uma sessão do PowerShell. Em seguida, utilize o seguinte exemplo para autenticar com a sua conta Run As e especificar uma subscrição:

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Defina uma tabela hash de agenda. Eis um exemplo de como deve ser construído:

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. Defina os parâmetros que são necessários pelo runbook. No exemplo seguinte, iremos como objetivo uma VM:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    Se estiver a etiquetagem um grupo de recursos, remova o *VMName* parâmetro do hash de $params tabela da seguinte forma:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. Execute o runbook de adicionar ResourceSchedule com os parâmetros seguintes para criar a etiqueta da agenda:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. Para atualizar uma etiqueta de máquina virtual ou grupo de recursos, execute o **atualização ResourceSchedule** runbook com os seguintes parâmetros:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>Remover uma etiqueta de agenda com o PowerShell
1. Abra uma sessão do PowerShell e execute o seguinte para autenticar com a sua conta Run As e para selecionar e especifique uma subscrição:

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Defina os parâmetros que são necessários pelo runbook. No exemplo seguinte, iremos como objetivo uma VM:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    Se estiver a remover uma etiqueta de um grupo de recursos, remova o *VMName* parâmetro do hash de $params tabela da seguinte forma:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. Execute o runbook ResourceSchedule remover para remover a etiqueta da agenda:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. Para atualizar uma etiqueta de máquina virtual ou grupo de recursos, execute o runbook remover ResourceSchedule com os seguintes parâmetros:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> É recomendado é monitorizar proativamente estes runbooks (e os Estados da máquina virtual) para verificar que as máquinas virtuais estão a ser encerradas para baixo e iniciado em conformidade.
>

Para ver os detalhes da tarefa de runbook de teste ResourceSchedule no portal do Azure, selecione o **tarefas** mosaico do runbook. O resumo da tarefa apresenta os parâmetros de entrada e o fluxo de saída, para além de informações gerais sobre a tarefa e eventuais exceções, caso tenham ocorrido.

O **Resumo da Tarefa** inclui mensagens dos fluxos de saída, aviso e erro. Selecione o mosaico **Saída** para ver resultados detalhados da execução do runbook.

![Resultado do teste ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>Passos seguintes
* Para começar com runbooks do fluxo de trabalho do PowerShell, veja [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para obter mais informações sobre tipos de runbook e as vantagens e limitações, consulte [tipos de runbook da automatização do Azure](automation-runbook-types.md).
* Para obter mais informações sobre as funcionalidades de suporte de script do PowerShell, consulte [suporte de script de PowerShell nativo na automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Para saber mais sobre o registo de runbook e saída, consulte [Runbook resultados e mensagens na automatização do Azure](automation-runbook-output-and-messages.md).
* Para obter mais informações sobre uma conta Run As do Azure e como autenticar os runbooks ao utilizá-lo, consulte [autenticar runbooks com a conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
