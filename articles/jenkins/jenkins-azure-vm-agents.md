---
title: "Dimensione implementações no Jenkins com agentes de VM do Azure."
description: "Aumentar a capacidade para pipelines Jenkins com máquinas virtuais do Azure com o agente da VM Jenkins Azure Plug-in."
services: multiple
documentationcenter: 
author: rloutlaw
manager: justhe
ms.service: multiple
ms.workload: multiple
ms.topic: article
ms.date: 8/25/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: dbb30809ab68079666ecfa81a896c1d5101fb6fb
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Dimensionar as implementações de Jenkins para satisfazer o pedido com agentes de VM do Azure

Este tutorial mostra como utilizar o Jenkins [Plug-in de agentes de VM do Azure](https://plugins.jenkins.io/azure-vm-agents) adicionar capacidade a pedido com máquinas virtuais do Linux em execução no Azure.

Neste tutorial, irá:

> [!div class="checklist"]
> * Instalar o plug-in de agentes de VM do Azure
> * Configurar o plug-in para criar recursos na sua subscrição do Azure
> * Definir os recursos de computação disponíveis para cada agente
> * Definir o sistema operativo e as ferramentas instaladas em cada agente
> * Criar uma nova tarefa freestyle Jenkins
> * Execute a tarefa de um agente de VM do Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.
* Um servidor principal Jenkins. Se não tiver uma, ver o [início rápido](install-jenkins-solution-template.md) para configurar uma no Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Plugin dos agentes de VM do Azure para Jenkins

> [!TIP]
> Se implementou Jenkins no Azure com o [modelo de solução](install-jenkins-solution-template.md), o plug-in do agente da VM do Azure já está instalado.

1. A partir do Jenkins dashboard, selecione **gerir Jenkins**, em seguida, selecione **gerir plug-ins**.
2. Selecione o **disponível** separador, em seguida, procure **agentes de VM do Azure**. Selecione a caixa de verificação junto a entrada para o plug-in e selecione **instalar sem reinício** na parte inferior do dashboard.

## <a name="configure-the-azure-vm-agents-plugin"></a>Configurar o plug-in de agentes de VM do Azure

1. A partir do Jenkins dashboard, selecione **gerir Jenkins**, em seguida, **Configurar sistema**.
2. Desloque-se na parte inferior da página e localize o **nuvem** secção com o **Adicionar nova na nuvem** pendente e escolha **agentes do Microsoft Azure VM**.
3. Selecionar um principal de serviço existente do **adicionar** pendente no **credenciais do Azure** secção. Se nenhum estiver listado, execute os seguintes passos para [criar um principal de serviço](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) para o seu Azure conta e adicioná-lo à sua configuração Jenkins:   

    a. Selecione **adicionar** junto a **credenciais do Azure** e escolha **Jenkins**.   
    b. No **adicionar credenciais** caixa de diálogo, selecione **Principal de serviço do Microsoft Azure** do **tipo** pendente.   
    c. Criar um principal de serviço do Active Directory a partir da CLI do Azure ou [nuvem Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Introduza as credenciais do serviço principal para o **adicionar credenciais** caixa de diálogo. Se não souber o ID de subscrição do Azure, pode consultá-lo a partir da CLI do:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    O principal de serviço concluídos deve utilizar o `id` campo para **ID de subscrição**, a `appId` valor para **ID de cliente**, `password` para **segredo do cliente**e um URL para **ponto final de tokens de OAuth 2.0** de `https://login.windows.net/<tenant_value>`. Selecione **adicionar** para adicionar o principal de serviço e, em seguida, configurar o plug-in para utilizar a credencial recentemente criada.

    ![Configurar o Principal de serviço do Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. No **nome do grupo de recursos** secção, deixe **criar nova** selecionada e introduza `myJenkinsAgentGroup`.
5. Selecione **verifique configuração** para ligar ao Azure para testar as definições de perfil.
6. Selecione **aplicar** para atualizar a configuração de plug-in.

## <a name="configure-agent-resources"></a>Configurar recursos de agente

Configure um modelo para ser utilizado definir um agente de VM do Azure. Este modelo define os recursos de computação cada agente tem quando criou.

1. Selecione **adicionar** junto a **adicionar modelo de Máquina Virtual do Azure**.
2. Introduza `defaulttemplate` para o **nome**
3. Introduza `ubuntu` para o **etiqueta**
4. Selecione o pretendido [região do Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) da caixa de combinação.
5. Selecione um [tamanho da VM](/azure/virtual-machines/linux/sizes) da lista pendente em **tamanho da Máquina Virtual**. Uma para fins gerais `Standard_DS1_v2` tamanho é adequado para este tutorial.   
6. Deixe o **período de retenção** em `60`. Esta definição define o número de minutos que jenkins pode aguardar antes de desalocar-agentes inativos. Especifique 0 se não pretender que os agentes de inatividade seja automaticamente removida.

   ![Configuração de VM geral](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Configurar o sistema operativo de agente e ferramentas

No **imagem configuração** secção de configuração do plug-in, selecione **Ubuntu 16.04 LTS**. As caixas de verificação junto a **instalar o Git (mais recentes)**, **Maven instalar (V3.5.0)**, e **instalar Docker** para instalar estas ferramentas nos agentes recentemente criados.

![Configurar e ferramentas de SO de VM](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Selecione **adicionar** junto a **credenciais de administrador**, em seguida, selecione **Jenkins**. Introduza um nome de utilizador e palavra-passe utilizada para iniciar sessão para os agentes, certificando-se de que satisfazem o [política de nome de utilizador e palavra-passe](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) as contas administrativas em VMs do Azure.

Selecione **verificar modelo** para verificar a configuração e, em seguida, selecione **guardar** para guardar as alterações e regressar ao Jenkins dashboard.

## <a name="create-a-job-in-jenkins"></a>Criar uma tarefa no Jenkins

1. No painel do Jenkins, clique em **Novo item**. 
2. Introduza `demoproject1` para o nome e selecione **projeto Freestyle**, em seguida, selecione **OK**.
3. No **geral** separador, escolha **restringir onde pode ser executado projeto** e tipo `ubuntu` no **expressão da etiqueta**. Verá uma mensagem de confirmar que a etiqueta é fornecida na configuração da nuvem criada no passo anterior. 
   ![Configurar a tarefa](./media/jenkins-azure-vm-agents/job-config.png)
4. No **gestão da origem de código** separador, selecione **Git** e adicione o seguinte URL para o **URL do repositório** campo:`https://github.com/spring-projects/spring-petclinic.git`
5. No **criar** separador, selecione **Adicionar passo de compilação**, em seguida, **invocar destinos nível superior do Maven**. Introduza `package` no **objetivos** campo.
6. Selecione **guardar** para guardar a definição de tarefa.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Criar a nova tarefa num agente VM do Azure

1. Volte ao painel do Jenkins.
2. Selecione a tarefa que criou no passo anterior, em seguida, clique em **compilar agora**. Uma nova compilação é colocada na fila, mas não inicia até que um agente VM é criado na sua subscrição do Azure.
3. Quando a compilação estiver concluída, vá para **Saída da consola**. Pode ver que a compilação foi efetuada remotamente num agente do Azure.

![Saída da consola](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [CI/CD para o App Service do Azure](java-deploy-webapp-tutorial.md)
