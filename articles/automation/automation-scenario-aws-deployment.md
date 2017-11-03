---
title: "Automatizar a implementação de uma VM nos Amazon Web Services | Microsoft Docs"
description: "Este artigo demonstra como utilizar a automatização do Azure para automatizar a criação de uma VM de serviço do Amazon Web"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 1d85c01a-d795-4523-8194-84fc15b53838
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: tiandert; bwren
ms.openlocfilehash: 828f9e2cc9a39e54933cd0e0db7273efa460d0c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Cenário de automatização do Azure - aprovisionar uma máquina virtual do AWS
Neste artigo, vamos demonstrar como pode tirar partido da automatização do Azure para Aprovisionar uma máquina virtual na sua subscrição do Amazon Web Service (AWS) e dar um nome específico – que AWS refere-se a como "marcação" a VM dessa VM.

## <a name="prerequisites"></a>Pré-requisitos
Para efeitos deste artigo, tem de ter uma conta de automatização do Azure e uma subscrição do AWS. Para mais informações sobre como configurar uma conta de automatização do Azure e configurá-lo com as suas credenciais de subscrição do AWS, reveja [configurar a autenticação com os Amazon Web Services](automation-config-aws-account.md).  Esta conta deve ser criada ou atualizada com as suas credenciais de subscrição do AWS antes de continuar, como podemos fará referência esta conta nos passos abaixo.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implementar o módulo PowerShell do Amazon Web Services
O módulo do PowerShell do AWS para realizar o trabalho irá tirar partido da nossa VM aprovisionamento runbook. Execute os seguintes passos para adicionar o módulo para a sua conta de automatização que esteja configurada com as suas credenciais de subscrição do AWS.  

1. Abra o browser e navegue para o [galeria do PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/) e clique em de **implementar no botão de automatização do Azure**.<br><br> ![Importação do módulo do AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. É direcionado para a página de início de sessão do Azure e após a autenticação, será encaminhado para o portal do Azure e lhe apresentada a página seguinte.<br><br> ![Página de módulos de importação](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione o grupo de recursos do **grupo de recursos** pendente lista e no painel de parâmetros, forneça as seguintes informações:
   
   * Do **novo ou a conta de automatização existente (cadeia)** selecione na lista pendente **existentes**.  
   * No **nome da conta de automatização (cadeia)** caixa, escreva o nome exato da conta de automatização que inclui as credenciais para a sua subscrição do AWS.  Por exemplo, se tiver criado uma conta dedicada com o nome **AWSAutomation**, em seguida, o que é escreva na caixa.
   * Selecione a região de adequado a **localização da conta de automatização** na lista pendente.
4. Quando tiver terminado de introduzir as informações necessárias, clique em **criar**.
   
   > [!NOTE]
   > Ao importar um módulo do PowerShell na automatização do Azure, este é também extrair os cmdlets e estas atividades não serão apresentado até que o módulo completamente terminou de importar e extrair os cmdlets. Este processo pode demorar alguns minutos.  
   > <br>
   > 
   > 
5. No portal do Azure, abra a sua conta de automatização referenciada no passo 3.
6. Clique em de **ativos** mosaico e no **ativos** painel, selecione o **módulos** mosaico.
7. No **módulos** página, verá o **AWSPowerShell** módulo na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar AWS implementar runbook VM
Depois de ter sido implementado o módulo do PowerShell AWS, pode agora criamos um runbook para automatizar o aprovisionamento de uma máquina virtual no AWS através de um script do PowerShell. Os passos abaixo demonstrar como tirar partido do script do PowerShell nativo na automatização do Azure.  

> [!NOTE]
> Para mais opções e informações sobre este script, visite o [galeria do PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Transferir o script do PowerShell New-AwsVM da galeria do PowerShell, abra uma sessão do PowerShell e escrevendo o seguinte:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. A partir do portal do Azure, abra a conta de automatização e selecione **Runbooks** na secção **automatização de processos** à esquerda.  
3. Do **Runbooks** página, selecione **adicionar um runbook**.
4. No **adicionar um runbook** painel, selecione **criação rápida** (criar um runbook novo).
5. No **Runbook** painel de propriedades, escreva um nome na caixa de nome para o runbook e do **tipo de Runbook** selecione na lista pendente **PowerShell**e, em seguida, clique em **Criar**.<br><br> ![Criar painel do runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Quando for apresentada a página Editar Runbook do PowerShell, copie e cole o script do PowerShell para o runbook criação tela.<br><br> ![Script do PowerShell do Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Tenha em atenção o seguinte ao trabalhar com o script do PowerShell de exemplo:
    > 
    > * O runbook contém um número de valores de parâmetro predefinidos. Volte a avaliar todos os valores predefinidos e atualizar sempre que necessário.
    > * Se tiver armazenado as suas credenciais do AWS como um recurso de credencial denominado de forma diferente que **AWScred**, terá de atualizar o script de linha 57 para corresponder em conformidade.  
    > * Ao trabalhar com os comandos da CLI do AWS no PowerShell, especialmente com este runbook de exemplo, tem de especificar a região do AWS. Caso contrário, os cmdlets irá falhar.  Tópico AWS vista [especificar região do AWS](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) nas ferramentas do AWS para documentos do PowerShell para obter mais detalhes.  
    >

7. Para obter uma lista de nomes de imagem da sua subscrição do AWS, inicie o ISE do PowerShell e importe o módulo PowerShell do AWS.  Autenticar face AWS, substituindo **Get-AutomationPSCredential** no seu ambiente de ISE com **AWScred = Get-Credential**.  Isto irá solicitar-lhe as suas credenciais e pode fornecer o **ID da chave de acesso** para o nome de utilizador e **chave de acesso secreta** a palavra-passe.  Consulte o exemplo abaixo:  

        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile

    É devolvido o seguinte resultado:<br><br>
   ![Obter imagens AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Copiar e colar uma variável de automatização como referenciadas no runbook como um dos nomes de imagens **$InstanceType**. Uma vez que este exemplo estamos a utilizar o AWS livre camadas subscrição, iremos utilizar **t2.micro** para o nosso exemplo de runbook.  
9. Guarde o runbook, em seguida, clique em **publicar** para publicar o runbook e, em seguida, **Sim** quando lhe for pedido.

### <a name="testing-the-aws-vm-runbook"></a>Testar o runbook AWS VM
Antes de prosseguir iremos testar o runbook, temos de verificar algumas coisas. Especificamente:  

* Um recurso de autenticação relativamente aos AWS foi criado chamado **AWScred** ou o script foi atualizado para referenciar o nome do recurso de credencial.    
* O módulo PowerShell do AWS foi importado na automatização do Azure  
* Foi criado um novo runbook e os valores de parâmetros foram verificados e atualizados sempre que necessário  
* **Criar registos verbosos** e, opcionalmente, **criar registos de progressos** sob a definição de runbook **registo e rastreio** foram definidos para **no**.<br><br> ![Registo do Runbook e o rastreio](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Queremos iniciar o runbook, por isso, clique em **iniciar** e, em seguida, clique em **OK** quando abre o Painel Iniciar Runbook.
2. No Painel Iniciar Runbook, forneça um **VMname**.  Aceite os valores predefinidos para os outros parâmetros pré-configurada anteriormente no script.  Clique em **OK** para iniciar a tarefa de runbook.<br><br> ![Iniciar AwsVM novo runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. É aberto um painel de tarefas para a tarefa de runbook que acabamos de criar. Feche este painel.
4. Iremos pode ver o progresso da tarefa e ver a saída **fluxos** selecionando o **todos os registos** mosaico da página de tarefa de runbook.<br><br> ![Saída de fluxo](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Para confirmar que a VM está a ser aprovisionada, iniciar a sessão na consola de gestão do AWS se não tem sessão iniciada.<br><br> ![Consola AWS implementado VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passos seguintes
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

