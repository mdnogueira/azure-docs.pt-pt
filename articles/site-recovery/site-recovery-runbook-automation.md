---
title: "Adicionar runbooks de automatização do Azure para planos de recuperação no Azure Site Recovery | Microsoft Docs"
description: "Saiba como do Azure Site Recovery pode ajudá-lo expandir planos de recuperação através da automatização do Azure. Saiba como concluir tarefas complexas durante a recuperação para o Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: af5de1c262bc55b1aa7513ca91b68eb50b44dbb7
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar runbooks de automatização do Azure para planos de recuperação
Neste artigo, vamos descrever como o Azure Site Recovery se integra com a automatização do Azure para o ajudar a expandir os planos de recuperação. Planos de recuperação podem orquestrar a recuperação de VMs que estão protegidos com a recuperação de Site. Funcionam planos de recuperação para a replicação para uma nuvem secundária e para replicação no Azure. Planos de recuperação também ajudam a efetuar a recuperação **consistentemente exata**, **repetíveis**, e **automatizada**. Se a ativação pós-falha as suas VMs no Azure, a integração com a automatização do Azure expande os planos de recuperação. Pode utilizá-lo para executar runbooks, o que oferece tarefas de automatização de elevado desempenho.

Se estiver familiarizado com a automatização do Azure, pode [inscrever](https://azure.microsoft.com/services/automation/) e [transfira scripts de exemplo](https://azure.microsoft.com/documentation/scripts/). Para obter mais informações e para saber como orquestrar recuperação para o Azure utilizando [planos de recuperação](https://azure.microsoft.com/blog/?p=166264), consulte [do Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

Neste artigo, vamos descrever a forma como pode integrar os runbooks de automatização do Azure os planos de recuperação. Utilizamos exemplos para automatizar tarefas básicas que anteriormente necessitavam de intervenção manual. Podemos também descrevem como converter uma recuperação de vários passo para uma ação de recuperação de clique único.

## <a name="customize-the-recovery-plan"></a>Personalizar o plano de recuperação
1. Vá para o **recuperação de Site** painel de recursos do plano de recuperação. Para este exemplo, o plano de recuperação tem duas VMs adicionadas ao mesmo, para recuperação. Para começar a adicionar um runbook, clique em de **personalizar** separador.

    ![Clique no botão Personalizar](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Clique com botão direito **grupo 1: Iniciar**e, em seguida, selecione **post adicionar ação**.

    ![Contexto grupo 1: Iniciar e adicionar post ação](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Clique em **escolher um script**.

4. No **atualizar ação** painel, o nome do script **Olá, mundo**.

    ![O painel de ação de atualização](media/site-recovery-runbook-automation-new/update-rp.png)

5. Introduza um nome de conta de automatização.
    >[!NOTE]
    > A conta de automatização pode estar em qualquer região do Azure. A conta de automatização tem de ser na mesma subscrição do cofre do Azure Site Recovery.

6. Na sua conta de automatização, selecione um runbook. Este runbook é o script que é executado durante a execução do plano de recuperação, após a recuperação do primeiro grupo.

7. Para guardar o script, clique em **OK**. O script é adicionado à **grupo 1: pós-passos**.

    ![Grupo de pós-ação de 1:Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Considerações para adicionar um script

* Para conhecer as opções para **eliminar um passo** ou **atualizar o script**, faça duplo clique o script.
* Um script pode executar no Azure durante a ativação pós-falha, a partir de uma máquina no local para o Azure. -Também pode executar no Azure como um script de site principal antes de encerramento, durante a reativação pós-falha a partir do Azure para uma máquina no local.
* Quando executa um script, injects um contexto de plano de recuperação. O exemplo seguinte mostra uma variável de contexto:

    ```
            {"RecoveryPlanName":"hrweb-recovery",

            "FailoverType":"Test",

            "FailoverDirection":"PrimaryToSecondary",

            "GroupId":"1",

            "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",

                    "ResourceGroupName":"ContosoRG",

                    "CloudServiceName":"pod02hrweb-Chicago-test",

                    "RoleName":"Fabrikam-Hrweb-frontend-test",

                    "RecoveryPointId":"TimeStamp"}

                    }

            }
    ```

    A tabela seguinte lista o nome e descrição de cada variável no contexto.

    | **Nome da variável** | **Descrição** |
    | --- | --- |
    | RecoveryPlanName |O nome do plano a ser executado. Esta variável ajuda a efetuar ações diferentes com base no nome do plano de recuperação. Também pode reutilizar o script. |
    | FailoverType |Especifica se a ativação pós-falha é um teste, planeada ou não planeada. |
    | FailoverDirection |Especifica se a recuperação é a um site primário ou secundário. |
    | GroupID |Identifica o número de grupo no plano de recuperação quando o plano está em execução. |
    | VmMap |Uma matriz de todas as VMs no grupo. |
    | Chave de VMMap |Uma chave exclusiva (GUID) para cada VM. É o mesmo que o ID de Azure Virtual Machine Manager (VMM) da VM, onde for aplicável. |
    | SubscriptionId |O ID de subscrição do Azure na qual a VM foi criada. |
    | RoleName |O nome da VM do Azure que está a ser recuperada. |
    | CloudServiceName |O nome do serviço em nuvem do Azure no qual a VM foi criada. |
    | resourceGroupName|O nome do grupo de recursos do Azure no qual a VM foi criada. |
    | RecoveryPointId|Timestamp para quando a VM é recuperada. |

* Certifique-se de que a conta de automatização tem os seguintes módulos:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Todos os módulos devem ser das versões compatíveis. É uma forma fácil de garantir que todos os módulos são compatíveis para utilizar as versões mais recentes de todos os módulos.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Aceder a todas as VMs de VMMap num ciclo
Utilize o seguinte código para cíclicas em todas as VMs de VMMap da Microsoft:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
 foreach($VMID in $VMinfo)
 {
     $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
         #this check is to ensure that we skip when some data is not available else it will fail
 Write-output "Resource group name ", $VM.ResourceGroupName
 Write-output "Rolename " = $VM.RoleName
     }
 }

```

> [!NOTE]
> Os recurso grupo e função de valores de nome estão vazios quando o script é uma pré-ação a um grupo de arranque. Os valores são preenchidos apenas se a VM desse grupo for bem sucedida na ativação pós-falha. O script é uma pós-ação de do grupo de arranque.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Utilize o mesmo runbook de automatização vários planos de recuperação

Pode utilizar um script único nos vários planos de recuperação através da utilização de variáveis externas. Pode utilizar [variáveis da automatização do Azure](../automation/automation-variables.md) para armazenar os parâmetros que podem passar para uma execução de plano de recuperação. Ao adicionar o nome do plano de recuperação como um prefixo para a variável, pode criar variáveis individuais para cada plano de recuperação. Em seguida, utilize as variáveis como parâmetros. Pode alterar o parâmetro sem alterar o script, mas ainda alterar a forma como o script funciona.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Utilizar uma variável de cadeia simples num runbook script

Neste exemplo, um script demora a entrada de um grupo de segurança de rede (NSG) e aplica-se-lo para as VMs de um plano de recuperação.

Para o script detetar que recuperação está a executar o plano, utilize o contexto do plano de recuperação:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Para aplicar um NSG existente, tem de saber o nome do NSG e o nome de grupo de recursos NSG. Utilize estas variáveis como entradas para os scripts de plano de recuperação. Para tal, crie duas variáveis nos ativos de conta de automatização. Adicione o nome do plano de recuperação que está a criar os parâmetros como prefixo do nome da variável.

1. Crie uma variável para armazenar o nome NSG. Adicione um prefixo para o nome da variável com o nome do plano de recuperação.

    ![Criar uma variável de nome do NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Crie uma variável para armazenar o nome do grupo de recursos do NSG. Adicione um prefixo para o nome da variável com o nome do plano de recuperação.

    ![Criar um nome de grupo de recursos do NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  No script, utilize o seguinte código de referência para obter os valores das variáveis:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Utilize as variáveis no runbook para aplicar o NSG à interface de rede da VM efetuada a ativação pós-falha:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```

Para cada plano de recuperação, crie variáveis independentes, de modo a que possa reutilizar o script. Adicione um prefixo com o nome do plano de recuperação. Para um script completado, ponto a ponto para este cenário, consulte [adicionar um IP público e um NSG a VMs durante a ativação pós-falha de teste de um plano de recuperação do Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Utilizar uma variável de complexas para armazenar mais informações

Considere um cenário no qual pretende que um script único para ativar um IP público em VMs específicos. Outro cenário, poderá pretender aplicar NSGs diferentes em diferentes VMs (e não em todas as VMs). Pode efetuar um script que é reutilizável para qualquer plano de recuperação. Cada plano de recuperação pode ter um número de VMs de variável. Por exemplo, uma recuperação do SharePoint tem dois front-ends. Uma aplicação básica linha de negócio (LOB) tem apenas um front-end. Não é possível criar variáveis separadas para cada plano de recuperação. 

No exemplo seguinte, iremos utilizar uma novo técnica e criar um [complexas variável](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) nos ativos de conta de automatização do Azure. Fazê-lo especificando valores múltiplos. Tem de utilizar o Azure PowerShell para concluir os seguintes passos:

1. No PowerShell, inicie sessão sua subscrição do Azure:

    ```
    login-azurermaccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Para armazenar os parâmetros, crie a variável de complexas utilizando o nome do plano de recuperação:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Nesta variável complexas, **VMDetails** é o ID de VM para a VM protegida. Para obter o ID de VM, no portal do Azure, consulte as propriedades VM. A seguinte captura de ecrã mostra uma variável que armazena os detalhes de duas VMs:

    ![Utilizar o ID de VM como o GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Utilize esta variável no runbook. Se o GUID de VM indicados for encontrado no contexto do plano de recuperação, aplicar o NSG na VM:

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. No runbook, percorrer as VMs do contexto do plano de recuperação. Verifique se a VM existe no **$VMDetailsObj**. Se existir, aceder às propriedades da variável para aplicar o NSG:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Pode utilizar o mesmo script para planos de recuperação diferente. Introduza diferentes parâmetros ao armazenar o valor que corresponde a um plano de recuperação em diferentes variáveis.

## <a name="sample-scripts"></a>Scripts de exemplo

Para implementar scripts de exemplo para a sua conta de automatização, clique o **implementar no Azure** botão.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Para obter outro exemplo, veja o vídeo seguinte. -Demonstra como recuperar uma aplicação WordPress de duas camadas para o Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="additional-resources"></a>Recursos adicionais
* [Conta do Azure Automation serviço Run As](../automation/automation-sec-configure-azure-runas-account.md)
* [Descrição geral de automatização do Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "descrição geral da automatização do Azure")
* [Scripts de exemplo de automatização do Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "scripts de exemplo da automatização do Azure")
