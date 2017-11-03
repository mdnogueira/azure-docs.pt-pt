---
title: "Introdução ao Azure Automation DSC | Microsoft Docs"
description: "Explicação e exemplos das tarefas mais comuns no Automation DSC do Azure pretendido Estado Configuration)"
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;eslesar
ms.openlocfilehash: 8a10d961ad7c107c68b57c64ee6c88544ff8832b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-automation-dsc"></a>Introdução ao Azure Automation DSC
Este tópico explica como efetuar tarefas mais comuns com Automation DSC do Azure pretendido Estado Configuration (), tais como criar, importar e configurações de máquinas de integração para gerir, a compilação e visualizar relatórios. Para obter uma descrição geral sobre o DSC de automatização do Azure é, consulte [descrição geral do Azure Automation DSC](automation-dsc-overview.md). Para obter documentação de DSC, consulte [Windows PowerShell Desired Configuration descrição geral do estado](https://msdn.microsoft.com/PowerShell/dsc/overview).

Este tópico fornece um guia passo a passo para utilizar o DSC de automatização do Azure. Se pretender que um ambiente de exemplo que já está definido sem seguir os passos descritos neste tópico, pode utilizar [o modelo ARM seguinte](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup). Este modelo configura um ambiente de DSC de automatização do Azure foi concluído, incluindo uma VM do Azure que é gerido pelo Automation DSC do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir os exemplos neste tópico, é necessário o seguinte:

* Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
* VM do Azure Resource Manager (não clássica) com o Windows Server 2008 R2 ou posterior. Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Criar uma configuração de DSC
Iremos criar uma simples [configuração DSC](https://msdn.microsoft.com/powershell/dsc/configurations) que garante a presença ou ausência de **servidor Web** Windows funcionalidade (IIS), dependendo de como pode designar nós.

1. Inicie o Windows PowerShell ISE (ou qualquer editor de texto).
2. Escreva o seguinte texto:
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
   
            }
        }
   
        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
   
            }
        }
        }
    ```
3. Guarde o ficheiro como `TestConfig.ps1`.

Esta configuração chama um recurso no bloco de cada nó, o [WindowsFeature recursos](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), que garante a presença ou ausência de **servidor Web** funcionalidade.

## <a name="importing-a-configuration-into-azure-automation"></a>Importar uma configuração para a automatização do Azure
Em seguida, iremos irá importar a configuração para a conta de automatização.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **configurações de DSC**.
4. No **configurações de DSC** painel, clique em **adicionar uma configuração**.
5. No **importar a configuração** painel, navegue para o `TestConfig.ps1` ficheiros no seu computador.
   
    ![Captura de ecrã do * * painel importar configuração * *](./media/automation-dsc-getting-started/AddConfig.png)
6. Clique em **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Uma configuração de visualização na automatização do Azure
Depois de ter importado uma configuração, que o possa visualizar no portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **configurações de DSC**
4. No **configurações de DSC** painel, clique em **TestConfig** (este é o nome da configuração que importou no procedimento anterior).
5. No **TestConfig configuração** painel, clique em **origem de configuração de vista**.
   
    ![Captura de ecrã do painel de configuração de TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    A **origem de configuração de TestConfig** abre painel, que apresenta o código do PowerShell para a configuração.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilar uma configuração na automatização do Azure
Antes de poder aplicar um estado pretendido para um nó, uma configuração de DSC definir esse Estado tem de ter compilar uma ou mais configurações de nó (documento MOF) e colocada no servidor de solicitação do DSC de automatização. Para obter uma descrição mais detalhada de compilar configurações de DSC de automatização do Azure, consulte [compilar configurações de DSC de automatização do Azure](automation-dsc-compile.md). Para obter mais informações sobre configurações de compilação, consulte [configurações de DSC](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **configurações de DSC**
4. No **configurações de DSC** painel, clique em **TestConfig** (o nome da configuração importado anteriormente).
5. No **TestConfig configuração** painel, clique em **compilar**e, em seguida, clique em **Sim**. Esta ação inicia uma tarefa de compilação.
   
    ![Captura de ecrã do painel de configuração de TestConfig realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando compilar uma configuração na automatização do Azure, implementa automaticamente qualquer configuração de nó criado MOFs para o servidor de solicitação.
> 
> 

## <a name="viewing-a-compilation-job"></a>Ver uma tarefa de compilação
Depois de iniciar uma compilação, pode vê-la no **tarefas de compilação** na peça de mosaico do **configuração** painel. O **tarefas de compilação** mosaico mostra atualmente em execução, concluir e tarefas com falha. Quando abre um painel de tarefas de compilação, mostra informações sobre essa tarefa, incluindo quaisquer erros ou avisos encontrou, os parâmetros de entrada utilizado na configuração e compilação registos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **configurações de DSC**.
4. No **configurações de DSC** painel, clique em **TestConfig** (o nome da configuração importado anteriormente).
5. No **tarefas de compilação** mosaico do **TestConfig configuração** painel, clique em qualquer uma das tarefas listadas. A **tarefa de compilação** é aberto o painel, com a etiqueta com a data em que a tarefa de compilação foi iniciada.
   
    ![Captura de ecrã do painel de tarefa de compilação](./media/automation-dsc-getting-started/CompilationJob.png)
6. Clique em qualquer mosaico no **tarefa de compilação** painel para ver mais detalhes sobre a tarefa.

## <a name="viewing-node-configurations"></a>Visualizar configurações de nó
A conclusão com êxito de uma tarefa de compilação cria uma ou mais configurações de nó novo. Uma configuração de nó é um documento MOF que é implementado para o servidor de solicitação e pronto para ser solicitados e aplicada por um ou mais nós. Pode ver as configurações de nó na sua conta de automatização no **configurações de nó DSC** painel. Uma configuração de nó tem um nome com o formato *ConfigurationName*. *NodeName*.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **configurações de nó DSC**.
   
    ![Captura de ecrã do painel de configurações de nó DSC](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Integração de VM do Azure para a gestão com o Automation DSC do Azure
Pode utilizar o DSC de automatização do Azure para gerir VMs do Azure (clássica e Resource Manager), VMs no local, máquinas Linux, VMs do AWS e máquinas físicas no local. Este tópico abrange como apenas Resource Manager VMS do Azure. Para obter informações sobre a integração de outros tipos de máquinas, consulte [máquinas de integração para gestão pelo Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Integrar VM do Azure Resource Manager para gestão pelo Automation DSC do Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **nós de DSC**.
4. No **nós de DSC** painel, clique em **adicionar a VM do Azure**.
   
    ![Captura de ecrã do painel de nós de DSC realçando o botão de adicionar a VM do Azure](./media/automation-dsc-getting-started/OnboardVM.png)
5. No **adicionar VMs do Azure** painel, clique em **selecionar máquinas virtuais para carregar**.
6. No **selecione VMs** painel, selecione a VM que pretende carregar e clique em **OK**.
   
   > [!IMPORTANT]
   > Tem de ser uma VM de Gestor de recursos do Azure com o Windows Server 2008 R2 ou posterior.
   > 
   > 
7. No **adicionar VMs do Azure** painel, clique em **configurar dados de registo**.
8. No **registo** painel, introduza o nome da configuração do nó que pretende aplicar para a VM com o **nome de configuração de nó** caixa. Isto deve corresponder exatamente o nome de uma configuração de nó na conta de automatização. Neste momento a fornecer um nome é opcional. Pode alterar a configuração do nó atribuído após o nó de integração.
   Verifique **reiniciar nó, se for necessário**e, em seguida, clique em **OK**.
   
    ![Captura de ecrã do painel registo](./media/automation-dsc-getting-started/RegisterVM.png)
   
    A configuração do nó que especificou será aplicada à VM em intervalos especificados pelo **frequência de modo de configuração**, e a VM irá procurar atualizações para a configuração do nó em intervalos especificados pelo **frequência de atualização**. Para obter mais informações sobre a forma como estes valores são utilizados, consulte [configurar o Gestor de configuração Local](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
9. No **adicionar VMs do Azure** painel, clique em **criar**.

Azure iniciará o processo de integração da VM. Quando estiver concluída, a VM aparecerá no **nós de DSC** painel na conta de automatização.

## <a name="viewing-the-list-of-dsc-nodes"></a>Ver a lista de nós de DSC
Pode ver a lista de todas as máquinas que foram integradas para a gestão da sua conta de automatização no **nós de DSC** painel.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **nós de DSC**.

## <a name="viewing-reports-for-dsc-nodes"></a>Visualizar relatórios para nós de DSC
Sempre que Automation DSC do Azure efetua uma verificação de consistência num nó gerido, o nó envia um relatório de estado para o servidor de solicitação. Pode ver estes relatórios no painel para esse nó.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **nós de DSC**.
4. No **relatórios** mosaico, clique em qualquer um dos relatórios na lista.
   
    ![Captura de ecrã do painel do relatório](./media/automation-dsc-getting-started/NodeReport.png)

No painel de um relatório individuais, pode ver as seguintes informações de estado para a verificação de consistência correspondente:

* O estado do relatório — se o nó é "Compatíveis", a configuração de "Falhado", ou o nó é "Não compatíveis" (quando o nó está a ser **applyandmonitor** modo e a máquina não está no estado pretendido).
* A hora de início para a verificação de consistência.
* O runtime total para a verificação de consistência.
* O tipo de verificação de consistência.
* Quaisquer erros, incluindo o código de erro e a mensagem de erro. 
* Quaisquer recursos de DSC utilizados na configuração e o estado de cada recurso (se o nó está no estado pretendido para esse recurso) — pode clicar em cada recurso para obter informações mais detalhadas para esse recurso.
* O nome, o endereço IP e o modo de configuração do nó.

Também pode clicar em **ver o relatório não processado** para ver os dados reais que envia o nó para o servidor. Para obter mais informações sobre como utilizar os dados, consulte [utilizando um servidor de relatório de DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Pode demorar algum tempo depois de um nó integrado antes do primeiro relatório está disponível. Poderá ter de aguardar até 30 minutos para que o primeiro relatório depois de carregar um nó.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Reatribuição de um nó para uma configuração de nó diferente
Pode atribuir um nó para utilizar uma configuração de nó diferente daquela que inicialmente atribuída.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **nós de DSC**.
4. No **nós de DSC** painel, clique no nome do nó que pretende reatribuir.
5. No painel nesse nó, clique em **atribuir nó**.
   
    ![Captura de ecrã do painel do nó, realçando o botão de atribuir nó](./media/automation-dsc-getting-started/AssignNode.png)
6. No **atribuir a configuração do nó** painel, selecione a configuração do nó para o qual pretende atribuir o nó e, em seguida, clique em **OK**.
   
    ![Captura de ecrã do painel de atribuir a configuração do nó](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Anulação do registo do nó
Se pretender que já não é um nó para serem geridos pelo Automation DSC do Azure, pode anular registo-lo.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
3. No **conta de automatização** painel, clique em **nós de DSC**.
4. No **nós de DSC** painel, clique no nome do nó que pretende anular o registo.
5. No painel nesse nó, clique em **Unregister**.
   
    ![Captura de ecrã do painel do nó, realçando o botão Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Artigos relacionados
* [Descrição geral do DSC da automatização do Azure](automation-dsc-overview.md)
* [Máquinas de integração de gestão do Automation DSC do Azure](automation-dsc-onboarding.md)
* [Descrição geral da configuração do estado pretendido do Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/overview)
* [Cmdlets do DSC da automatização do Azure](/powershell/module/azurerm.automation/#automation)
* [Preços do DSC da automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)

